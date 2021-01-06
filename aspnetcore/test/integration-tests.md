---
title: Tests d’intégration dans ASP.NET Core
author: rick-anderson
description: Découvrez comment les tests d’intégration garantissent le bon fonctionnement des composants d’une application au niveau de l’infrastructure, notamment la base de données, le système de fichiers et le réseau.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
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
uid: test/integration-tests
ms.openlocfilehash: f1ce6a209ef3ca85abe0a6f1ac61d85bec52d17a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93050821"
---
# <a name="integration-tests-in-aspnet-core"></a>Tests d’intégration dans ASP.NET Core

Par [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)et [Jos van der til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Les tests d’intégration garantissent que les composants d’une application fonctionnent correctement à un niveau qui comprend l’infrastructure de prise en charge de l’application, telle que la base de données, le système de fichiers et le réseau. ASP.NET Core prend en charge les tests d’intégration à l’aide d’une infrastructure de tests unitaires avec un hôte Web de test et un serveur de test en mémoire.

Cette rubrique suppose une compréhension de base des tests unitaires. Si vous n’êtes pas familiarisé avec les concepts de test, consultez la rubrique [tests unitaires dans .net Core et .NET standard](/dotnet/core/testing/) et son contenu lié.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple d’application est une Razor application pages et suppose une compréhension de base des Razor pages. Si vous n’êtes pas familiarisé avec Razor les pages, consultez les rubriques suivantes :

* [Présentation des Razor pages](xref:razor-pages/index)
* [Prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start)
* [Razor Pages tests unitaires](xref:test/razor-pages-tests)

> [!NOTE]
> Pour tester la SPAs, nous vous recommandons d’utiliser un outil tel que le [sélénium](https://www.seleniumhq.org/), qui peut automatiser un navigateur.

## <a name="introduction-to-integration-tests"></a>Présentation des tests d’intégration

Les tests d’intégration évaluent les composants d’une application à un niveau plus large que les [tests unitaires](/dotnet/core/testing/). Les tests unitaires sont utilisés pour tester des composants logiciels isolés, tels que des méthodes de classe individuelles. Les tests d’intégration confirment que deux ou plusieurs composants d’application fonctionnent ensemble pour produire un résultat attendu, y compris éventuellement chaque composant requis pour traiter entièrement une demande.

Ces tests plus larges sont utilisés pour tester l’infrastructure de l’application et l’infrastructure entière, y compris les composants suivants :

* Base de données
* Système de fichiers
* Appliances réseau
* Pipeline de requête-réponse

Les tests unitaires utilisent des composants fabriqués, appelés objets *substituts* ou *factices*, à la place des composants d’infrastructure.

Contrairement aux tests unitaires, les tests d’intégration :

* Utilisez les composants réels utilisés par l’application en production.
* Nécessitent davantage de code et de traitement des données.
* Prend plus de temps pour s’exécuter.

Par conséquent, limitez l’utilisation des tests d’intégration aux scénarios d’infrastructure les plus importants. Si un comportement peut être testé à l’aide d’un test unitaire ou d’un test d’intégration, choisissez le test unitaire.

> [!TIP]
> N’écrivez pas de tests d’intégration pour chaque permutation possible d’accès aux données et aux fichiers avec les bases de données et les systèmes de fichiers. Quel que soit le nombre d’emplacements au sein d’une application qui interagissent avec les bases de données et les systèmes de fichiers, un ensemble de tests d’intégration de lecture, d’écriture, de mise à jour et de suppression est généralement en mesure de tester correctement les composants de base de données et de système de fichiers. Utilisez des tests unitaires pour les tests de routine de la logique de méthode qui interagissent avec ces composants. Dans les tests unitaires, l’utilisation de substituts ou de simulacres d’infrastructure entraîne une exécution plus rapide des tests.

> [!NOTE]
> Dans les discussions sur les tests d’intégration, le projet testé est fréquemment appelé le *système en cours de test* ou « St » pour l’instant.
>
> *« St » est utilisé dans cette rubrique pour faire référence à l’application ASP.NET Core testée.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core les tests d’intégration

Les tests d’intégration dans ASP.NET Core requièrent les éléments suivants :

* Un projet de test est utilisé pour contenir et exécuter les tests. Le projet de test a une référence à St.
* Le projet de test crée un hôte Web de test pour le St et utilise un client de serveur de test pour gérer les demandes et les réponses avec le St.
* Un testeur de test est utilisé pour exécuter les tests et signaler les résultats des tests.

Les tests d’intégration suivent une séquence d’événements qui incluent les étapes de test *arrange*, *Act* et *Assert* habituelles :

1. L’hôte Web de St est configuré.
1. Un client de serveur de test est créé pour envoyer des demandes à l’application.
1. L’étape *organiser* le test est exécutée : l’application de test prépare une demande.
1. L’étape de test *Act* est exécutée : le client envoie la demande et reçoit la réponse.
1. L’étape de test d' *assertion* est exécutée : la réponse *réelle* est validée en tant que *réussite* ou *échec* en fonction d’une réponse *attendue* .
1. Le processus se poursuit jusqu’à ce que tous les tests soient exécutés.
1. Les résultats des tests sont signalés.

En règle générale, l’hôte Web de test est configuré différemment de l’hôte Web normal de l’application pour les séries de tests. Par exemple, une base de données différente ou des paramètres d’application différents peuvent être utilisés pour les tests.

Les composants d’infrastructure, tels que l’hôte Web de test et le serveur de test en mémoire ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), sont fournis ou gérés par le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) . L’utilisation de ce package simplifie la création et l’exécution des tests.

Le `Microsoft.AspNetCore.Mvc.Testing` package gère les tâches suivantes :

* Copie le fichier de dépendances (*. DEPS*) à partir du St dans le répertoire *bin* du projet de test.
* Définit la racine du [contenu](xref:fundamentals/index#content-root) sur la racine du projet St pour que les fichiers et les pages statiques soient trouvés lors de l’exécution des tests.
* Fournit la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pour simplifier l’amorçage du St avec `TestServer` .

La documentation sur les [tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) décrit comment configurer un projet de test et Test Runner, ainsi que des instructions détaillées sur la façon d’exécuter des tests et des recommandations sur la façon de nommer des tests et des classes de test.

> [!NOTE]
> Lorsque vous créez un projet de test pour une application, séparez les tests unitaires des tests d’intégration dans différents projets. Cela permet de s’assurer que les composants de test d’infrastructure ne sont pas accidentellement inclus dans les tests unitaires. La séparation des tests d’unité et d’intégration permet également de contrôler l’ensemble des tests exécutés.

Il n’existe pratiquement aucune différence entre la configuration des tests d' Razor applications de pages et les applications MVC. La seule différence réside dans la façon dont les tests sont nommés. Dans une Razor application de pages, les tests des points de terminaison de page sont généralement nommés après la classe de modèle de page (par exemple, `IndexPageTests` pour tester l’intégration des composants pour la page d’index). Dans une application MVC, les tests sont généralement organisés par classes de contrôleur et nommés après les contrôleurs testés (par exemple, `HomeControllerTests` pour tester l’intégration des composants pour le contrôleur de démarrage).

## <a name="test-app-prerequisites"></a>Conditions préalables pour les applications de test

Le projet de test doit :

* Référencez le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .
* Spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ).

Ces conditions préalables peuvent être consultées dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Inspectez le fichier *tests/ Razor PagesProject. tests/ Razor PagesProject. tests. csproj* . L’exemple d’application utilise l’infrastructure de test [xUnit](https://xunit.github.io/) et la bibliothèque de l’analyseur [AngleSharp](https://anglesharp.github.io/) , de sorte que l’exemple d’application référence également :

* [xUnit](https://www.nuget.org/packages/xunit)
* [xUnit. Runner. VisualStudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core est également utilisé dans les tests. L’application référence :

* [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft. AspNetCore. Identity . EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>Environnement St

Si l' [environnement](xref:fundamentals/environments) de St n’est pas défini, l’environnement est par défaut développement.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Tests de base avec le WebApplicationFactory par défaut

[WebApplicationFactory \<TEntryPoint> ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) est utilisé pour créer un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pour les tests d’intégration. `TEntryPoint` est la classe de point d’entrée de St, généralement la `Startup` classe.

Les classes de test implémentent une interface de *contexte de classe* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) pour indiquer que la classe contient des tests et fournir des instances d’objet partagé dans les tests de la classe.

La classe de test suivante, `BasicTests` , utilise le `WebApplicationFactory` pour démarrer le St et fournir un [httpclient](/dotnet/api/system.net.http.httpclient) à une méthode de test, `Get_EndpointsReturnSuccessAndCorrectContentType` . La méthode vérifie si le code d’état de réponse a réussi (codes d’État dans la plage 200-299) et si l' `Content-Type` en-tête est `text/html; charset=utf-8` pour plusieurs pages d’application.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crée une instance de `HttpClient` qui suit automatiquement les redirections et les handles cookie .

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Par défaut, cookie les s non essentiels ne sont pas conservées entre les demandes lorsque la [stratégie de consentement RGPD](xref:security/gdpr) est activée. Pour conserver les s non essentiels cookie , tels que ceux utilisés par le fournisseur TempData, marquez-les comme essentiels dans vos tests. Pour obtenir des instructions sur le marquage d’un cookie comme essentiel, consultez la rubrique [Essential cookie s](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personnaliser WebApplicationFactory

La configuration d’hôte Web peut être créée indépendamment des classes de test en héritant de `WebApplicationFactory` pour créer une ou plusieurs fabriques personnalisées :

1. Hériter de `WebApplicationFactory` et substituer [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permet la configuration de la collection de services avec [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   L’amorçage de base de données dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) est effectué par la `InitializeDbForTests` méthode. La méthode est décrite dans la section [exemple de tests d’intégration : organisation](#test-app-organization) d’une application de test.

   Le contexte de base de données de St est inscrit dans sa `Startup.ConfigureServices` méthode. Le rappel de l’application de test `builder.ConfigureServices` est exécuté *après* l’exécution du code de l’application `Startup.ConfigureServices` . L’ordre d’exécution est une modification avec rupture pour l' [hôte générique](xref:fundamentals/host/generic-host) avec la version de ASP.net Core 3,0. Pour utiliser une base de données différente pour les tests que la base de données de l’application, le contexte de base de données de l’application doit être remplacé dans `builder.ConfigureServices` .

   Pour les SUTs qui utilisent toujours l' [hôte Web](xref:fundamentals/host/web-host), le rappel de l’application de test `builder.ConfigureServices` est exécuté *avant* le code du St `Startup.ConfigureServices` . Le rappel de l’application de test `builder.ConfigureTestServices` est exécuté *après*.

   L’exemple d’application recherche le descripteur de service pour le contexte de base de données et utilise le descripteur pour supprimer l’inscription du service. Ensuite, la fabrique ajoute un nouveau `ApplicationDbContext` qui utilise une base de données en mémoire pour les tests.

   Pour vous connecter à une base de données différente de celle de la base de données en mémoire, modifiez l' `UseInMemoryDatabase` appel pour connecter le contexte à une autre base de données. Pour utiliser une base de données de test SQL Server :

   * Référencez le package NuGet [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) dans le fichier projet.
   * Appelez `UseSqlServer` avec une chaîne de connexion à la base de données.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Utilisez le personnalisé `CustomWebApplicationFactory` dans les classes de test. L’exemple suivant utilise la fabrique dans la `IndexPageTests` classe :

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Le client de l’exemple d’application est configuré pour empêcher la `HttpClient` suite de redirections. Comme expliqué plus loin dans la section [d’authentification des simulacres](#mock-authentication) , cela permet aux tests de vérifier le résultat de la première réponse de l’application. La première réponse est une redirection dans un grand nombre de ces tests avec un `Location` en-tête.

3. Un test standard utilise les `HttpClient` méthodes d’assistance et pour traiter la demande et la réponse :

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Toute demande de publication à l’St doit être conforme à la vérification anti-contrefaçon effectuée automatiquement par le [système anti-contrefaçon de protection des données](xref:security/data-protection/introduction)de l’application. Pour organiser la requête de publication d’un test, l’application de test doit :

1. Effectuez une demande pour la page.
1. Analysez l’anti-contrefaçon cookie et le jeton de validation de demande à partir de la réponse.
1. Effectuez la demande de publication avec l’anti-contrefaçon cookie et le jeton de validation de demande en place.

Les `SendAsync` méthodes d’extension d’assistance (*helpers/HttpClientExtensions. cs*) et la `GetDocumentAsync` méthode d’assistance (*helpers/HtmlHelper. cs*) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) utilisent l’analyseur [AngleSharp](https://anglesharp.github.io/) pour gérer la vérification anti-falsification avec les méthodes suivantes :

* `GetDocumentAsync`: Reçoit le [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) et retourne un `IHtmlDocument` . `GetDocumentAsync` utilise une fabrique qui prépare une *réponse virtuelle* en fonction de l’original `HttpResponseMessage` . Pour plus d’informations, consultez la [documentation AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync` les méthodes d’extension pour `HttpClient` compose un [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) et appellent [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pour envoyer des demandes au St. Les surcharges pour `SendAsync` acceptent le formulaire HTML ( `IHtmlFormElement` ) et les éléments suivants :
  * Bouton envoyer du formulaire ( `IHtmlElement` )
  * Collection de valeurs de formulaire ( `IEnumerable<KeyValuePair<string, string>>` )
  * Bouton envoyer ( `IHtmlElement` ) et valeurs de formulaire ( `IEnumerable<KeyValuePair<string, string>>` )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) est une bibliothèque d’analyse tierce utilisée à des fins de démonstration dans cette rubrique et l’exemple d’application. AngleSharp n’est pas pris en charge ou requis pour le test d’intégration des applications ASP.NET Core. D’autres analyseurs peuvent être utilisés, tels que le [HAP (html agilité Pack)](https://html-agility-pack.net/). Une autre approche consiste à écrire du code pour gérer directement le jeton de vérification de demande du système anti-contrefaçon et l’anti-contrefaçon cookie .

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personnaliser le client avec WithWebHostBuilder

Quand une configuration supplémentaire est requise dans une méthode de test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crée un nouveau `WebApplicationFactory` avec un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) qui est ensuite personnalisé par la configuration.

La `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode de test de l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) montre l’utilisation de `WithWebHostBuilder` . Ce test effectue une suppression d’enregistrement dans la base de données en déclenchant une soumission de formulaire dans St.

Étant donné qu’un autre test de la `IndexPageTests` classe effectue une opération qui supprime tous les enregistrements de la base de données et peut s’exécuter avant la `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode, la base de données est réamorcée dans cette méthode de test pour s’assurer qu’un enregistrement est présent pour que le St puisse être supprimé. La sélection du premier bouton supprimer du `messages` formulaire dans le St est simulée dans la requête adressée au St :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Options du client

Le tableau suivant indique les [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) par défaut disponibles lors de la création d' `HttpClient` instances.

| Option | Description | Default |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtient ou définit une valeur indiquant si les `HttpClient` instances de doivent suivre automatiquement les réponses de redirection. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtient ou définit l’adresse de base des `HttpClient` instances. | `http://localhost` |
| [Handle Cookie s](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtient ou définit si les `HttpClient` instances doivent gérer les cookie s. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtient ou définit le nombre maximal de réponses de redirection que les `HttpClient` instances doivent suivre. | 7 |

Créez la `WebApplicationFactoryClientOptions` classe et transmettez-la à la méthode [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (les valeurs par défaut sont affichées dans l’exemple de code) :

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Injecter des services fictifs

Les services peuvent être substitués dans un test à l’aide d’un appel à [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) sur le générateur de l’hôte. **Pour injecter des services fictifs, St doit avoir une `Startup` classe avec une `Startup.ConfigureServices` méthode.**

L’exemple de St comprend un service étendu qui retourne un guillemet. Le guillemet est incorporé dans un champ masqué sur la page d’index lorsque la page d’index est demandée.

*Services/IQuoteService. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs* :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/index. cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Le balisage suivant est généré lors de l’exécution de l’application St :

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Pour tester l’injection de service et de guillemets dans un test d’intégration, un service fictif est injecté dans le St par le test. Le service factice remplace l’application par `QuoteService` un service fourni par l’application de test, appelé `TestQuoteService` :

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` est appelé, et le service étendu est inscrit :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Le balisage produit pendant l’exécution du test reflète le texte du devis fourni par `TestQuoteService` , donc l’assertion réussit :

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Simuler l’authentification

Les tests de la `AuthTests` classe vérifient qu’un point de terminaison sécurisé :

* Redirige un utilisateur non authentifié vers la page de connexion de l’application.
* Retourne le contenu d’un utilisateur authentifié.

Dans St, la `/SecurePage` page utilise une convention [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) pour appliquer un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à la page. Pour plus d’informations, consultez [ Razor conventions d’autorisation des pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Dans le `Get_SecurePageRedirectsAnUnauthenticatedUser` test, un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) est défini pour interdire les redirections en affectant à [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) la valeur `false` :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

En interautorisant le client à suivre la redirection, vous pouvez effectuer les vérifications suivantes :

* Le code d’état retourné par le St peut être vérifié par rapport au résultat [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) attendu, pas le code d’état final après la redirection vers la page de connexion, qui serait [HttpStatusCode. OK](/dotnet/api/system.net.httpstatuscode).
* La `Location` valeur d’en-tête dans les en-têtes de réponse est vérifiée pour confirmer qu’elle commence par `http://localhost/Identity/Account/Login` , et non par la réponse de page de connexion finale, où l' `Location` en-tête n’est pas présent.

L’application de test peut simuler un <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> dans [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) afin de tester les aspects de l’authentification et de l’autorisation. Un scénario minimal renvoie un [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

La `TestAuthHandler` méthode est appelée pour authentifier un utilisateur lorsque le schéma d’authentification a la valeur `Test` où `AddAuthentication` est inscrit pour `ConfigureTestServices` . Il est important que le `Test` schéma corresponde au schéma attendu par votre application. Dans le cas contraire, l’authentification ne fonctionnera pas.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Pour plus d’informations sur `WebApplicationFactoryClientOptions` , consultez la section [Options du client](#client-options) .

## <a name="set-the-environment"></a>Définir l’environnement

Par défaut, l’environnement de l’hôte et de l’application de St est configuré pour utiliser l’environnement de développement. Pour remplacer l’environnement de St lors de l’utilisation de `IHostBuilder` :

* Définissez la `ASPNETCORE_ENVIRONMENT` variable d’environnement (par exemple,, `Staging` `Production` ou une autre valeur personnalisée, telle que `Testing` ).
* Remplacez `CreateHostBuilder` dans l’application de test pour lire les variables d’environnement précédées de `ASPNETCORE` .

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

Si le St utilise l’hôte Web ( `IWebHostBuilder` ), remplacez `CreateWebHostBuilder` :

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Comment l’infrastructure de test déduit le chemin racine du contenu de l’application

Le `WebApplicationFactory` constructeur déduit le chemin d’accès [racine du contenu](xref:fundamentals/index#content-root) de l’application en recherchant un [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) sur l’assembly contenant les tests d’intégration avec une clé égale à l' `TEntryPoint` assembly `System.Reflection.Assembly.FullName` . Si un attribut avec la clé correcte est introuvable, revient `WebApplicationFactory` à la recherche d’un fichier solution (*. sln*) et ajoute le `TEntryPoint` nom de l’assembly au répertoire de la solution. Le répertoire racine de l’application (chemin racine du contenu) est utilisé pour découvrir les affichages et les fichiers de contenu.

## <a name="disable-shadow-copying"></a>Désactiver les clichés instantanés

Les clichés instantanés provoquent l’exécution des tests dans un répertoire différent de celui du répertoire de sortie. Pour que les tests fonctionnent correctement, les clichés instantanés doivent être désactivés. L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) utilise xUnit et désactive les clichés instantanés pour xUnit en incluant un *xunit.runner.jssur* le fichier avec le paramètre de configuration correct. Pour plus d’informations, consultez [configuration de xUnit avec JSON](https://xunit.github.io/docs/configuring-with-json.html).

Ajoutez le *xunit.runner.jssur* le fichier à la racine du projet de test avec le contenu suivant :

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Suppression d’objets

Après l’exécution des tests de l' `IClassFixture` implémentation, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) et [httpclient](/dotnet/api/system.net.http.httpclient) sont supprimés quand xUnit supprime le [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Si les objets instanciés par le développeur nécessitent une suppression, supprimez-les dans l' `IClassFixture` implémentation. Pour plus d’informations, consultez [implémentation d’une méthode dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Exemple de tests d’intégration

L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se compose de deux applications :

| Application | Répertoire du projet | Description |
| --- | ----------------- | ----------- |
| Application message (St) | *SRC/ Razor PagesProject* | Permet à l’utilisateur d’ajouter, de supprimer, de supprimer et d’analyser des messages. |
| Tester une application | *tests/ Razor PagesProject. tests* | Utilisé pour tester l’intégration du St. |

Les tests peuvent être exécutés à l’aide des fonctionnalités de test intégrées d’un environnement de développement intégré (IDE), telles que [Visual Studio](https://visualstudio.microsoft.com). Si vous utilisez [Visual Studio code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à l’invite de commandes dans le répertoire *tests/ Razor PagesProject. tests* :

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organisation message App (St)

Le St est un Razor système de messagerie des pages avec les caractéristiques suivantes :

* La page d’index de l’application (*pages/index. cshtml* et *pages/index. cshtml. cs*) fournit une interface utilisateur et des méthodes de modèle de page pour contrôler l’ajout, la suppression et l’analyse de messages (mots moyens par message).
* Un message est décrit par la `Message` classe (*Data/message. cs*) avec deux propriétés : `Id` (Key) et `Text` (message). La `Text` propriété est obligatoire et limitée à 200 caractères.
* Les messages sont stockés à l’aide [de la base de données en mémoire de Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* L’application contient une couche d’accès aux données (DAL) dans sa classe de contexte de base de données, `AppDbContext` (*Data/AppDbContext. cs*).
* Si la base de données est vide au démarrage de l’application, la Banque de messages est initialisée avec trois messages.
* L’application comprend un `/SecurePage` qui n’est accessible qu’à un utilisateur authentifié.

&#8224;la rubrique EF [test with InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest. Cette rubrique utilise l’infrastructure de test [xUnit](https://xunit.github.io/) . Les concepts de test et les implémentations de test dans différents frameworks de test sont similaires, mais pas identiques.

Bien que l’application n’utilise pas le modèle de référentiel et ne soit pas un exemple efficace de [modèle d’unité de travail (UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), les Razor pages prennent en charge ces modèles de développement. Pour plus d’informations, consultez [conception de la couche de persistance de l’infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) et [logique du contrôleur de test](../mvc/controllers/testing.md) (l’exemple implémente le modèle de référentiel).

### <a name="test-app-organization"></a>Organisation des applications de test

L’application de test est une application console dans le répertoire *tests/ Razor PagesProject. tests* .

| Tester le répertoire de l’application | Description |
| ------------------ | ----------- |
| *AuthTests* | Contient des méthodes de test pour :<ul><li>Accès à une page sécurisée par un utilisateur non authentifié.</li><li>Accès à une page sécurisée par un utilisateur authentifié avec un simulacre <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</li><li>Obtention d’un profil utilisateur GitHub et vérification de la connexion de l’utilisateur du profil.</li></ul> |
| *BasicTests* | Contient une méthode de test pour le routage et le type de contenu. |
| *IntegrationTests* | Contient les tests d’intégration de la page d’index à l’aide de la `WebApplicationFactory` classe personnalisée. |
| *Programmes d’assistance/utilitaires* | <ul><li>*Utilities.cs* contient la `InitializeDbForTests` méthode utilisée pour amorcer la base de données avec des données de test.</li><li>*HtmlHelpers.cs* fournit une méthode pour retourner un AngleSharp `IHtmlDocument` pour une utilisation par les méthodes de test.</li><li>*HttpClientExtensions.cs* fournissent des surcharges pour `SendAsync` Envoyer des demandes au St.</li></ul> |

L’infrastructure de test est [xUnit](https://xunit.github.io/). Les tests d’intégration sont effectués à l’aide de [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), qui comprend le [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Étant donné que le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) est utilisé pour configurer l’hôte de test et le serveur de test, les `TestHost` `TestServer` packages et ne nécessitent pas de références de package directes dans le fichier projet ou la configuration de développeur de l’application de test dans l’application de test.

**Amorçage de la base de données à des fins de test**

Les tests d’intégration nécessitent généralement un petit jeu de données dans la base de données avant l’exécution du test. Par exemple, un test Delete requiert la suppression d’un enregistrement de base de données, de sorte que la base de données doit avoir au moins un enregistrement pour que la demande Delete aboutisse.

L’exemple d’application amorce la base de données avec trois messages dans *Utilities.cs* que les tests peuvent utiliser lorsqu’ils exécutent :

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

Le contexte de base de données de St est inscrit dans sa `Startup.ConfigureServices` méthode. Le rappel de l’application de test `builder.ConfigureServices` est exécuté *après* l’exécution du code de l’application `Startup.ConfigureServices` . Pour utiliser une base de données différente pour les tests, le contexte de base de données de l’application doit être remplacé dans `builder.ConfigureServices` . Pour plus d’informations, consultez la section [personnaliser WebApplicationFactory](#customize-webapplicationfactory) .

Pour les SUTs qui utilisent toujours l' [hôte Web](xref:fundamentals/host/web-host), le rappel de l’application de test `builder.ConfigureServices` est exécuté *avant* le code du St `Startup.ConfigureServices` . Le rappel de l’application de test `builder.ConfigureTestServices` est exécuté *après*.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Les tests d’intégration garantissent que les composants d’une application fonctionnent correctement à un niveau qui comprend l’infrastructure de prise en charge de l’application, telle que la base de données, le système de fichiers et le réseau. ASP.NET Core prend en charge les tests d’intégration à l’aide d’une infrastructure de tests unitaires avec un hôte Web de test et un serveur de test en mémoire.

Cette rubrique suppose une compréhension de base des tests unitaires. Si vous n’êtes pas familiarisé avec les concepts de test, consultez la rubrique [tests unitaires dans .net Core et .NET standard](/dotnet/core/testing/) et son contenu lié.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

L’exemple d’application est une Razor application pages et suppose une compréhension de base des Razor pages. Si vous n’êtes pas familiarisé avec Razor les pages, consultez les rubriques suivantes :

* [Présentation des Razor pages](xref:razor-pages/index)
* [Prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start)
* [Razor Pages tests unitaires](xref:test/razor-pages-tests)

> [!NOTE]
> Pour tester la SPAs, nous vous recommandons d’utiliser un outil tel que le [sélénium](https://www.seleniumhq.org/), qui peut automatiser un navigateur.

## <a name="introduction-to-integration-tests"></a>Présentation des tests d’intégration

Les tests d’intégration évaluent les composants d’une application à un niveau plus large que les [tests unitaires](/dotnet/core/testing/). Les tests unitaires sont utilisés pour tester des composants logiciels isolés, tels que des méthodes de classe individuelles. Les tests d’intégration confirment que deux ou plusieurs composants d’application fonctionnent ensemble pour produire un résultat attendu, y compris éventuellement chaque composant requis pour traiter entièrement une demande.

Ces tests plus larges sont utilisés pour tester l’infrastructure de l’application et l’infrastructure entière, y compris les composants suivants :

* Base de données
* Système de fichiers
* Appliances réseau
* Pipeline de requête-réponse

Les tests unitaires utilisent des composants fabriqués, appelés objets *substituts* ou *factices*, à la place des composants d’infrastructure.

Contrairement aux tests unitaires, les tests d’intégration :

* Utilisez les composants réels utilisés par l’application en production.
* Nécessitent davantage de code et de traitement des données.
* Prend plus de temps pour s’exécuter.

Par conséquent, limitez l’utilisation des tests d’intégration aux scénarios d’infrastructure les plus importants. Si un comportement peut être testé à l’aide d’un test unitaire ou d’un test d’intégration, choisissez le test unitaire.

> [!TIP]
> N’écrivez pas de tests d’intégration pour chaque permutation possible d’accès aux données et aux fichiers avec les bases de données et les systèmes de fichiers. Quel que soit le nombre d’emplacements au sein d’une application qui interagissent avec les bases de données et les systèmes de fichiers, un ensemble de tests d’intégration de lecture, d’écriture, de mise à jour et de suppression est généralement en mesure de tester correctement les composants de base de données et de système de fichiers. Utilisez des tests unitaires pour les tests de routine de la logique de méthode qui interagissent avec ces composants. Dans les tests unitaires, l’utilisation de substituts ou de simulacres d’infrastructure entraîne une exécution plus rapide des tests.

> [!NOTE]
> Dans les discussions sur les tests d’intégration, le projet testé est fréquemment appelé le *système en cours de test* ou « St » pour l’instant.
>
> *« St » est utilisé dans cette rubrique pour faire référence à l’application ASP.NET Core testée.*

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core les tests d’intégration

Les tests d’intégration dans ASP.NET Core requièrent les éléments suivants :

* Un projet de test est utilisé pour contenir et exécuter les tests. Le projet de test a une référence à St.
* Le projet de test crée un hôte Web de test pour le St et utilise un client de serveur de test pour gérer les demandes et les réponses avec le St.
* Un testeur de test est utilisé pour exécuter les tests et signaler les résultats des tests.

Les tests d’intégration suivent une séquence d’événements qui incluent les étapes de test *arrange*, *Act* et *Assert* habituelles :

1. L’hôte Web de St est configuré.
1. Un client de serveur de test est créé pour envoyer des demandes à l’application.
1. L’étape *organiser* le test est exécutée : l’application de test prépare une demande.
1. L’étape de test *Act* est exécutée : le client envoie la demande et reçoit la réponse.
1. L’étape de test d' *assertion* est exécutée : la réponse *réelle* est validée en tant que *réussite* ou *échec* en fonction d’une réponse *attendue* .
1. Le processus se poursuit jusqu’à ce que tous les tests soient exécutés.
1. Les résultats des tests sont signalés.

En règle générale, l’hôte Web de test est configuré différemment de l’hôte Web normal de l’application pour les séries de tests. Par exemple, une base de données différente ou des paramètres d’application différents peuvent être utilisés pour les tests.

Les composants d’infrastructure, tels que l’hôte Web de test et le serveur de test en mémoire ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), sont fournis ou gérés par le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) . L’utilisation de ce package simplifie la création et l’exécution des tests.

Le `Microsoft.AspNetCore.Mvc.Testing` package gère les tâches suivantes :

* Copie le fichier de dépendances (*. DEPS*) à partir du St dans le répertoire *bin* du projet de test.
* Définit la racine du [contenu](xref:fundamentals/index#content-root) sur la racine du projet St pour que les fichiers et les pages statiques soient trouvés lors de l’exécution des tests.
* Fournit la classe [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) pour simplifier l’amorçage du St avec `TestServer` .

La documentation sur les [tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) décrit comment configurer un projet de test et Test Runner, ainsi que des instructions détaillées sur la façon d’exécuter des tests et des recommandations sur la façon de nommer des tests et des classes de test.

> [!NOTE]
> Lorsque vous créez un projet de test pour une application, séparez les tests unitaires des tests d’intégration dans différents projets. Cela permet de s’assurer que les composants de test d’infrastructure ne sont pas accidentellement inclus dans les tests unitaires. La séparation des tests d’unité et d’intégration permet également de contrôler l’ensemble des tests exécutés.

Il n’existe pratiquement aucune différence entre la configuration des tests d' Razor applications de pages et les applications MVC. La seule différence réside dans la façon dont les tests sont nommés. Dans une Razor application de pages, les tests des points de terminaison de page sont généralement nommés après la classe de modèle de page (par exemple, `IndexPageTests` pour tester l’intégration des composants pour la page d’index). Dans une application MVC, les tests sont généralement organisés par classes de contrôleur et nommés après les contrôleurs testés (par exemple, `HomeControllerTests` pour tester l’intégration des composants pour le contrôleur de démarrage).

## <a name="test-app-prerequisites"></a>Conditions préalables pour les applications de test

Le projet de test doit :

* Référencez les packages suivants :
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet ( `<Project Sdk="Microsoft.NET.Sdk.Web">` ). Le kit de développement logiciel (SDK) Web est requis pour référencer le [AspNetCore](xref:fundamentals/metapackage-app).

Ces conditions préalables peuvent être consultées dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Inspectez le fichier *tests/ Razor PagesProject. tests/ Razor PagesProject. tests. csproj* . L’exemple d’application utilise l’infrastructure de test [xUnit](https://xunit.github.io/) et la bibliothèque de l’analyseur [AngleSharp](https://anglesharp.github.io/) , de sorte que l’exemple d’application référence également :

* [xUnit](https://www.nuget.org/packages/xunit/)
* [xUnit. Runner. VisualStudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>Environnement St

Si l' [environnement](xref:fundamentals/environments) de St n’est pas défini, l’environnement est par défaut développement.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Tests de base avec le WebApplicationFactory par défaut

[WebApplicationFactory \<TEntryPoint> ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) est utilisé pour créer un [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) pour les tests d’intégration. `TEntryPoint` est la classe de point d’entrée de St, généralement la `Startup` classe.

Les classes de test implémentent une interface de *contexte de classe* ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) pour indiquer que la classe contient des tests et fournir des instances d’objet partagé dans les tests de la classe.

La classe de test suivante, `BasicTests` , utilise le `WebApplicationFactory` pour démarrer le St et fournir un [httpclient](/dotnet/api/system.net.http.httpclient) à une méthode de test, `Get_EndpointsReturnSuccessAndCorrectContentType` . La méthode vérifie si le code d’état de réponse a réussi (codes d’État dans la plage 200-299) et si l' `Content-Type` en-tête est `text/html; charset=utf-8` pour plusieurs pages d’application.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) crée une instance de `HttpClient` qui suit automatiquement les redirections et les handles cookie .

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Par défaut, cookie les s non essentiels ne sont pas conservées entre les demandes lorsque la [stratégie de consentement RGPD](xref:security/gdpr) est activée. Pour conserver les s non essentiels cookie , tels que ceux utilisés par le fournisseur TempData, marquez-les comme essentiels dans vos tests. Pour obtenir des instructions sur le marquage d’un cookie comme essentiel, consultez la rubrique [Essential cookie s](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Personnaliser WebApplicationFactory

La configuration d’hôte Web peut être créée indépendamment des classes de test en héritant de `WebApplicationFactory` pour créer une ou plusieurs fabriques personnalisées :

1. Hériter de `WebApplicationFactory` et substituer [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). Le [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permet la configuration de la collection de services avec [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), qui est exécuté avant l’application `Startup.ConfigureServices` . Le [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) permet de remplacer et de modifier les services inscrits dans la collection de services avec [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   L’amorçage de base de données dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) est effectué par la `InitializeDbForTests` méthode. La méthode est décrite dans la section [exemple de tests d’intégration : organisation](#test-app-organization) d’une application de test.

2. Utilisez le personnalisé `CustomWebApplicationFactory` dans les classes de test. L’exemple suivant utilise la fabrique dans la `IndexPageTests` classe :

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Le client de l’exemple d’application est configuré pour empêcher la `HttpClient` suite de redirections. Comme expliqué plus loin dans la section [d’authentification des simulacres](#mock-authentication) , cela permet aux tests de vérifier le résultat de la première réponse de l’application. La première réponse est une redirection dans un grand nombre de ces tests avec un `Location` en-tête.

3. Un test standard utilise les `HttpClient` méthodes d’assistance et pour traiter la demande et la réponse :

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Toute demande de publication à l’St doit être conforme à la vérification anti-contrefaçon effectuée automatiquement par le [système anti-contrefaçon de protection des données](xref:security/data-protection/introduction)de l’application. Pour organiser la requête de publication d’un test, l’application de test doit :

1. Effectuez une demande pour la page.
1. Analysez l’anti-contrefaçon cookie et le jeton de validation de demande à partir de la réponse.
1. Effectuez la demande de publication avec l’anti-contrefaçon cookie et le jeton de validation de demande en place.

Les `SendAsync` méthodes d’extension d’assistance (*helpers/HttpClientExtensions. cs*) et la `GetDocumentAsync` méthode d’assistance (*helpers/HtmlHelper. cs*) dans l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) utilisent l’analyseur [AngleSharp](https://anglesharp.github.io/) pour gérer la vérification anti-falsification avec les méthodes suivantes :

* `GetDocumentAsync`: Reçoit le [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) et retourne un `IHtmlDocument` . `GetDocumentAsync` utilise une fabrique qui prépare une *réponse virtuelle* en fonction de l’original `HttpResponseMessage` . Pour plus d’informations, consultez la [documentation AngleSharp](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync` les méthodes d’extension pour `HttpClient` compose un [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) et appellent [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) pour envoyer des demandes au St. Les surcharges pour `SendAsync` acceptent le formulaire HTML ( `IHtmlFormElement` ) et les éléments suivants :
  * Bouton envoyer du formulaire ( `IHtmlElement` )
  * Collection de valeurs de formulaire ( `IEnumerable<KeyValuePair<string, string>>` )
  * Bouton envoyer ( `IHtmlElement` ) et valeurs de formulaire ( `IEnumerable<KeyValuePair<string, string>>` )

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) est une bibliothèque d’analyse tierce utilisée à des fins de démonstration dans cette rubrique et l’exemple d’application. AngleSharp n’est pas pris en charge ou requis pour le test d’intégration des applications ASP.NET Core. D’autres analyseurs peuvent être utilisés, tels que le [HAP (html agilité Pack)](https://html-agility-pack.net/). Une autre approche consiste à écrire du code pour gérer directement le jeton de vérification de demande du système anti-contrefaçon et l’anti-contrefaçon cookie .

## <a name="customize-the-client-with-withwebhostbuilder"></a>Personnaliser le client avec WithWebHostBuilder

Quand une configuration supplémentaire est requise dans une méthode de test, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) crée un nouveau `WebApplicationFactory` avec un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) qui est ensuite personnalisé par la configuration.

La `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode de test de l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) montre l’utilisation de `WithWebHostBuilder` . Ce test effectue une suppression d’enregistrement dans la base de données en déclenchant une soumission de formulaire dans St.

Étant donné qu’un autre test de la `IndexPageTests` classe effectue une opération qui supprime tous les enregistrements de la base de données et peut s’exécuter avant la `Post_DeleteMessageHandler_ReturnsRedirectToRoot` méthode, la base de données est réamorcée dans cette méthode de test pour s’assurer qu’un enregistrement est présent pour que le St puisse être supprimé. La sélection du premier bouton supprimer du `messages` formulaire dans le St est simulée dans la requête adressée au St :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Options du client

Le tableau suivant indique les [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) par défaut disponibles lors de la création d' `HttpClient` instances.

| Option | Description | Default |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Obtient ou définit une valeur indiquant si les `HttpClient` instances de doivent suivre automatiquement les réponses de redirection. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Obtient ou définit l’adresse de base des `HttpClient` instances. | `http://localhost` |
| [Handle Cookie s](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Obtient ou définit si les `HttpClient` instances doivent gérer les cookie s. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Obtient ou définit le nombre maximal de réponses de redirection que les `HttpClient` instances doivent suivre. | 7 |

Créez la `WebApplicationFactoryClientOptions` classe et transmettez-la à la méthode [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (les valeurs par défaut sont affichées dans l’exemple de code) :

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Injecter des services fictifs

Les services peuvent être substitués dans un test à l’aide d’un appel à [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) sur le générateur de l’hôte. **Pour injecter des services fictifs, St doit avoir une `Startup` classe avec une `Startup.ConfigureServices` méthode.**

L’exemple de St comprend un service étendu qui retourne un guillemet. Le guillemet est incorporé dans un champ masqué sur la page d’index lorsque la page d’index est demandée.

*Services/IQuoteService. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs* :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/index. cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Le balisage suivant est généré lors de l’exécution de l’application St :

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Pour tester l’injection de service et de guillemets dans un test d’intégration, un service fictif est injecté dans le St par le test. Le service factice remplace l’application par `QuoteService` un service fourni par l’application de test, appelé `TestQuoteService` :

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` est appelé, et le service étendu est inscrit :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Le balisage produit pendant l’exécution du test reflète le texte du devis fourni par `TestQuoteService` , donc l’assertion réussit :

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Simuler l’authentification

Les tests de la `AuthTests` classe vérifient qu’un point de terminaison sécurisé :

* Redirige un utilisateur non authentifié vers la page de connexion de l’application.
* Retourne le contenu d’un utilisateur authentifié.

Dans St, la `/SecurePage` page utilise une convention [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) pour appliquer un [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) à la page. Pour plus d’informations, consultez [ Razor conventions d’autorisation des pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Dans le `Get_SecurePageRedirectsAnUnauthenticatedUser` test, un [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) est défini pour interdire les redirections en affectant à [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) la valeur `false` :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

En interautorisant le client à suivre la redirection, vous pouvez effectuer les vérifications suivantes :

* Le code d’état retourné par le St peut être vérifié par rapport au résultat [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) attendu, pas le code d’état final après la redirection vers la page de connexion, qui serait [HttpStatusCode. OK](/dotnet/api/system.net.httpstatuscode).
* La `Location` valeur d’en-tête dans les en-têtes de réponse est vérifiée pour confirmer qu’elle commence par `http://localhost/Identity/Account/Login` , et non par la réponse de page de connexion finale, où l' `Location` en-tête n’est pas présent.

L’application de test peut simuler un <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> dans [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) afin de tester les aspects de l’authentification et de l’autorisation. Un scénario minimal renvoie un [AuthenticateResult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

La `TestAuthHandler` méthode est appelée pour authentifier un utilisateur lorsque le schéma d’authentification est défini sur `Test` où `AddAuthentication` est inscrit pour `ConfigureTestServices` :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Pour plus d’informations sur `WebApplicationFactoryClientOptions` , consultez la section [Options du client](#client-options) .

## <a name="set-the-environment"></a>Définir l’environnement

Par défaut, l’environnement de l’hôte et de l’application de St est configuré pour utiliser l’environnement de développement. Pour remplacer l’environnement de St :

* Définissez la `ASPNETCORE_ENVIRONMENT` variable d’environnement (par exemple,, `Staging` `Production` ou une autre valeur personnalisée, telle que `Testing` ).
* Remplacez `CreateWebHostBuilder` dans l’application de test pour lire la `ASPNETCORE_ENVIRONMENT` variable d’environnement.

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

L’environnement peut également être défini directement sur le générateur de l’hôte dans un personnalisé <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> :

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Comment l’infrastructure de test déduit le chemin racine du contenu de l’application

Le `WebApplicationFactory` constructeur déduit le chemin d’accès [racine du contenu](xref:fundamentals/index#content-root) de l’application en recherchant un [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) sur l’assembly contenant les tests d’intégration avec une clé égale à l' `TEntryPoint` assembly `System.Reflection.Assembly.FullName` . Si un attribut avec la clé correcte est introuvable, revient `WebApplicationFactory` à la recherche d’un fichier solution (*. sln*) et ajoute le `TEntryPoint` nom de l’assembly au répertoire de la solution. Le répertoire racine de l’application (chemin racine du contenu) est utilisé pour découvrir les affichages et les fichiers de contenu.

## <a name="disable-shadow-copying"></a>Désactiver les clichés instantanés

Les clichés instantanés provoquent l’exécution des tests dans un répertoire différent de celui du répertoire de sortie. Pour que les tests fonctionnent correctement, les clichés instantanés doivent être désactivés. L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) utilise xUnit et désactive les clichés instantanés pour xUnit en incluant un *xunit.runner.jssur* le fichier avec le paramètre de configuration correct. Pour plus d’informations, consultez [configuration de xUnit avec JSON](https://xunit.github.io/docs/configuring-with-json.html).

Ajoutez le *xunit.runner.jssur* le fichier à la racine du projet de test avec le contenu suivant :

```json
{
  "shadowCopy": false
}
```

Si vous utilisez Visual Studio, définissez la propriété **copier dans le répertoire de sortie** du fichier sur **toujours copier**. Si vous n’utilisez pas Visual Studio, ajoutez une `Content` cible au fichier projet de l’application de test :

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Suppression d’objets

Après l’exécution des tests de l' `IClassFixture` implémentation, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) et [httpclient](/dotnet/api/system.net.http.httpclient) sont supprimés quand xUnit supprime le [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1). Si les objets instanciés par le développeur nécessitent une suppression, supprimez-les dans l' `IClassFixture` implémentation. Pour plus d’informations, consultez [implémentation d’une méthode dispose](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Exemple de tests d’intégration

L' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) se compose de deux applications :

| Application | Répertoire du projet | Description |
| --- | ----------------- | ----------- |
| Application message (St) | *SRC/ Razor PagesProject* | Permet à l’utilisateur d’ajouter, de supprimer, de supprimer et d’analyser des messages. |
| Tester une application | *tests/ Razor PagesProject. tests* | Utilisé pour tester l’intégration du St. |

Les tests peuvent être exécutés à l’aide des fonctionnalités de test intégrées d’un environnement de développement intégré (IDE), telles que [Visual Studio](https://visualstudio.microsoft.com). Si vous utilisez [Visual Studio code](https://code.visualstudio.com/) ou la ligne de commande, exécutez la commande suivante à l’invite de commandes dans le répertoire *tests/ Razor PagesProject. tests* :

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organisation message App (St)

Le St est un Razor système de messagerie des pages avec les caractéristiques suivantes :

* La page d’index de l’application (*pages/index. cshtml* et *pages/index. cshtml. cs*) fournit une interface utilisateur et des méthodes de modèle de page pour contrôler l’ajout, la suppression et l’analyse de messages (mots moyens par message).
* Un message est décrit par la `Message` classe (*Data/message. cs*) avec deux propriétés : `Id` (Key) et `Text` (message). La `Text` propriété est obligatoire et limitée à 200 caractères.
* Les messages sont stockés à l’aide [de la base de données en mémoire de Entity Framework](/ef/core/providers/in-memory/)&#8224;.
* L’application contient une couche d’accès aux données (DAL) dans sa classe de contexte de base de données, `AppDbContext` (*Data/AppDbContext. cs*).
* Si la base de données est vide au démarrage de l’application, la Banque de messages est initialisée avec trois messages.
* L’application comprend un `/SecurePage` qui n’est accessible qu’à un utilisateur authentifié.

&#8224;la rubrique EF [test with InMemory](/ef/core/miscellaneous/testing/in-memory), explique comment utiliser une base de données en mémoire pour les tests avec MSTest. Cette rubrique utilise l’infrastructure de test [xUnit](https://xunit.github.io/) . Les concepts de test et les implémentations de test dans différents frameworks de test sont similaires, mais pas identiques.

Bien que l’application n’utilise pas le modèle de référentiel et ne soit pas un exemple efficace de [modèle d’unité de travail (UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html), les Razor pages prennent en charge ces modèles de développement. Pour plus d’informations, consultez [conception de la couche de persistance de l’infrastructure](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) et [logique du contrôleur de test](../mvc/controllers/testing.md) (l’exemple implémente le modèle de référentiel).

### <a name="test-app-organization"></a>Organisation des applications de test

L’application de test est une application console dans le répertoire *tests/ Razor PagesProject. tests* .

| Tester le répertoire de l’application | Description |
| ------------------ | ----------- |
| *AuthTests* | Contient des méthodes de test pour :<ul><li>Accès à une page sécurisée par un utilisateur non authentifié.</li><li>Accès à une page sécurisée par un utilisateur authentifié avec un simulacre <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> .</li><li>Obtention d’un profil utilisateur GitHub et vérification de la connexion de l’utilisateur du profil.</li></ul> |
| *BasicTests* | Contient une méthode de test pour le routage et le type de contenu. |
| *IntegrationTests* | Contient les tests d’intégration de la page d’index à l’aide de la `WebApplicationFactory` classe personnalisée. |
| *Programmes d’assistance/utilitaires* | <ul><li>*Utilities.cs* contient la `InitializeDbForTests` méthode utilisée pour amorcer la base de données avec des données de test.</li><li>*HtmlHelpers.cs* fournit une méthode pour retourner un AngleSharp `IHtmlDocument` pour une utilisation par les méthodes de test.</li><li>*HttpClientExtensions.cs* fournissent des surcharges pour `SendAsync` Envoyer des demandes au St.</li></ul> |

L’infrastructure de test est [xUnit](https://xunit.github.io/). Les tests d’intégration sont effectués à l’aide de [Microsoft. AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost), qui comprend le [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Étant donné que le package [Microsoft. AspNetCore. Mvc. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) est utilisé pour configurer l’hôte de test et le serveur de test, les `TestHost` `TestServer` packages et ne nécessitent pas de références de package directes dans le fichier projet ou la configuration de développeur de l’application de test dans l’application de test.

**Amorçage de la base de données à des fins de test**

Les tests d’intégration nécessitent généralement un petit jeu de données dans la base de données avant l’exécution du test. Par exemple, un test Delete requiert la suppression d’un enregistrement de base de données, de sorte que la base de données doit avoir au moins un enregistrement pour que la demande Delete aboutisse.

L’exemple d’application amorce la base de données avec trois messages dans *Utilities.cs* que les tests peuvent utiliser lorsqu’ils exécutent :

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* [Tests unitaires](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>