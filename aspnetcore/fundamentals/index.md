---
title: Notions de base d’ASP.NET Core
author: rick-anderson
description: Découvrez les concepts de base permettant de créer des applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
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
uid: fundamentals/index
ms.openlocfilehash: 25348f8486ec6ccb53ebf527ad4519638dd5f73e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059375"
---
# <a name="aspnet-core-fundamentals"></a>Notions de base d’ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Cet article fournit une vue d’ensemble des rubriques clés pour comprendre comment développer des applications ASP.NET Core.

## <a name="the-startup-class"></a>Classe Startup

La classe `Startup` est l’endroit où :

* Les services nécessaires à l’application sont configurés.
* Le pipeline de traitement des demandes de l’application est défini comme une série de composants d’intergiciel (middleware).

Voici un exemple de classe `Startup` :

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Pour plus d'informations, consultez <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injection de dépendances (services)

ASP.NET Core intègre une infrastructure d’injection de dépendances (DI) intégrée qui rend les services configurés disponibles dans toute l’application. Par exemple, un composant de journalisation est un service.

Le code pour configurer (ou *enregistrer* ) des services est ajouté à la méthode `Startup.ConfigureServices`. Exemple :

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Les services sont généralement résolus à partir de DI à l’aide de l’injection de constructeur. Avec l’injection de constructeur, une classe déclare un paramètre de constructeur du type requis ou d’une interface. L’infrastructure DI fournit une instance de ce service au moment de l’exécution.

L’exemple suivant utilise l’injection de constructeur pour résoudre un `RazorPagesMovieContext` à partir de di :

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Si le conteneur inversion de contrôle (IoC) intégré ne répond pas à tous les besoins d’une application, un conteneur IoC tiers peut être utilisé à la place.

Pour plus d'informations, consultez <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Le pipeline de traitement des requêtes est composé comme une série de composants d’intergiciel (middleware). Chaque composant effectue des opérations sur un `HttpContext` et appelle l’intergiciel suivant dans le pipeline ou met fin à la requête.

Par Convention, un composant middleware est ajouté au pipeline en appelant une `Use...` méthode d’extension dans la `Startup.Configure` méthode. Par exemple, pour activer le rendu des fichiers statiques, appelez `UseStaticFiles`.

L’exemple suivant configure un pipeline de traitement des demandes :

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core comprend un ensemble complet d’intergiciels (middleware) intégrés. Vous pouvez également écrire des composants d’intergiciel (middleware) personnalisés.

Pour plus d'informations, consultez <xref:fundamentals/middleware/index>.

## <a name="host"></a>Hôte

Au démarrage, une application ASP.NET Core crée un *hôte* . L’hôte encapsule toutes les ressources de l’application, telles que :

* Une implémentation serveur HTTP
* Composants d’intergiciel (middleware)
* Journalisation
* Services d’injection de dépendance (DI)
* Configuration

Il existe deux hôtes différents : 

* Hôte générique .NET
* Hôte web ASP.NET Core

L’hôte générique .NET est recommandé. L’hôte Web ASP.NET Core est disponible uniquement à des fins de compatibilité descendante.

L’exemple suivant crée un hôte générique .NET :

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

Les `CreateDefaultBuilder` `ConfigureWebHostDefaults` méthodes et configurent un hôte avec un ensemble d’options par défaut, telles que :

* Utilisez [Kestrel](#servers) en tant que serveur web et activez l’intégration IIS.
* Charger la configuration à partir de *appsettings.json* , *appSettings. { Nom de l’environnement}. JSON* , les variables d’environnement, les arguments de ligne de commande et d’autres sources de configuration.
* Envoyez la sortie de journalisation aux fournisseurs Console et Debug.

Pour plus d'informations, consultez <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Scénarios non basés sur le web

L’hôte générique permet à d’autres types d’application d’utiliser des extensions de framework composites, par exemple la journalisation, l’injection de dépendance, la configuration et la gestion de la durée de vie de l’application. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host> et <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Serveurs

Une application ASP.NET Core utilise une implémentation de serveur HTTP pour écouter les requêtes HTTP. Les surfaces de serveur envoient des requêtes à l’application comme un ensemble de [fonctionnalités de requête](xref:fundamentals/request-features) composées dans un `HttpContext`.

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core fournit les implémentations de serveur suivantes :

* *Kestrel* est un serveur web multiplateforme. Kestrel est souvent exécuté dans une configuration de proxy inverse à l’aide d’[IIS](https://www.iis.net/). Dans ASP.NET Core 2.0 ou versions ultérieures, Kestrel peut être exécuté en tant que serveur de périphérie public exposé directement à Internet.
* Le *serveur http IIS* est un serveur pour Windows qui utilise IIS. Avec ce serveur, l’application ASP.NET Core et IIS s’exécutent dans le même processus.
* *HTTP.sys* est un serveur Windows qui n’est pas utilisé avec IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel* . Dans ASP.NET Core 2,0 ou version ultérieure, Kestrel peut s’exécuter en tant que serveur Edge public exposé directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel* . Dans ASP.NET Core 2,0 ou version ultérieure, Kestrel peut s’exécuter en tant que serveur Edge public exposé directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

Pour plus d'informations, consultez <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configuration

ASP.NET Core fournit une infrastructure de configuration qui obtient des paramètres en tant que paires nom-valeur à partir d’un ensemble ordonné de fournisseurs de configuration. Les fournisseurs de configuration intégrés sont disponibles pour diverses sources, telles que les fichiers *. JSON* , les fichiers *. xml* , les variables d’environnement et les arguments de ligne de commande. Écrivez des fournisseurs de configuration personnalisés pour prendre en charge d’autres sources.

Par [défaut](xref:fundamentals/configuration/index#default), les applications ASP.net Core sont configurées pour lire *appsettings.json* , les variables d’environnement, la ligne de commande, etc. Lorsque la configuration de l’application est chargée, les valeurs des variables d’environnement remplacent les valeurs de *appsettings.json* .

La méthode recommandée pour lire les valeurs de configuration associées utilise le [modèle d’options](xref:fundamentals/configuration/options). Pour plus d’informations, consultez [lier des données de configuration hiérarchiques à l’aide du modèle options](xref:fundamentals/configuration/index#optpat).

Pour la gestion des données de configuration confidentielles telles que les mots de passe, ASP.NET Core fournit le [Gestionnaire de secret](xref:security/app-secrets#secret-manager). Pour les secrets de production, nous vous recommandons [Azure Key Vault](xref:security/key-vault-configuration).

Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Environnements

Les environnements d’exécution, tels que `Development` , `Staging` et `Production` , sont une notion de première classe dans ASP.net core. Spécifiez l’environnement dans lequel une application s’exécute en définissant la `ASPNETCORE_ENVIRONMENT` variable d’environnement. ASP.NET Core lit la variable d’environnement au démarrage de l’application et stocke la valeur dans une implémentation `IWebHostEnvironment`. Cette implémentation est disponible n’importe où dans une application via l’injection de dépendances (DI).

L’exemple suivant configure l’application pour fournir des informations détaillées sur l’erreur lors de son exécution dans l' `Development` environnement :

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Pour plus d'informations, consultez <xref:fundamentals/environments>.

## <a name="logging"></a>Journalisation

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec un large éventail de fournisseurs de journalisation intégrés et tiers. Les fournisseurs disponibles sont les suivants :

* Console
* Débogage
* Suivi des événements sur Windows
* Journal des événements Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Pour créer des journaux, résolvez un <xref:Microsoft.Extensions.Logging.ILogger%601> service à partir d’une injection de dépendances et des méthodes de journalisation des appels telles que <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> . Exemple :

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Les méthodes de journalisation, telles que `LogInformation` prennent en charge un nombre quelconque de champs. Ces champs sont couramment utilisés pour construire un message `string` , mais certains fournisseurs de journalisation les envoient à un magasin de données en tant que champs distincts. Cette fonctionnalité permet aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Pour plus d'informations, consultez <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routage

Un *itinéraire* est un modèle d’URL qui est mappé à un gestionnaire. Le gestionnaire est généralement une Razor page, une méthode d’action dans un contrôleur MVC ou un intergiciel (middleware). Le routage ASP.NET Core vous permet de contrôler les URL utilisées par votre application.

Pour plus d'informations, consultez <xref:fundamentals/routing>.

## <a name="error-handling"></a>Gestion des erreurs

ASP.NET Core offre des fonctionnalités intégrées pour gérer des erreurs, telles que :

* Une page d’exceptions du développeur
* Pages d’erreur personnalisées
* Pages de codes d’état statique
* Gestion des exceptions de démarrage

Pour plus d'informations, consultez <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Effectuer des requêtes HTTP

Une implémentation de `IHttpClientFactory` est disponible pour la création d’instances `HttpClient`. La fabrique :

* Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques. Par exemple, inscrivez et configurez un client *GitHub* pour accéder à github. Inscrire et configurer un client par défaut à d’autres fins.
* Prend en charge l’inscription et le chaînage de plusieurs gestionnaires de délégation pour créer un pipeline de middlewares pour les requêtes sortantes. Ce modèle est similaire au pipeline de l’intergiciel (middleware) entrant de ASP.NET Core. Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux pour les requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.
* S’intègre à *Polly* , une bibliothèque tierce populaire pour la gestion des erreurs temporaires.
* Gère le regroupement et la durée de vie des instances sous-jacentes `HttpClientHandler` afin d’éviter les problèmes DNS courants qui se produisent lors de la gestion `HttpClient` manuelle des durées de vie.
* Ajoute une expérience de journalisation configurable via <xref:Microsoft.Extensions.Logging.ILogger> pour toutes les requêtes envoyées via les clients créés par la fabrique.

Pour plus d'informations, consultez <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Racine de contenu

La racine du contenu est le chemin de base pour :

* Exécutable qui héberge l’application ( *. exe* ).
* Assemblys compilés qui composent l’application ( *. dll* ).
* Fichiers de contenu utilisés par l’application, tels que :
  * Razor fichiers ( *. cshtml* , *. Razor* )
  * Fichiers de configuration ( *. JSON* , *. xml* )
  * Fichiers de données ( *. db* )
* [Racine Web](#web-root), généralement le dossier *wwwroot* .

Pendant le développement, la racine de contenu est par défaut le répertoire racine du projet. Ce répertoire est également le chemin d’accès de base pour les fichiers de contenu de l’application et la [racine Web](#web-root). Spécifiez une autre racine de contenu en définissant son chemin d’accès lors de [la génération de l’hôte](#host). Pour plus d’informations, consultez [Racine de contenu](xref:fundamentals/host/generic-host#contentroot).

## <a name="web-root"></a>Racine web

La racine Web est le chemin de base pour les fichiers de ressources statiques publics, tels que :

* Feuilles de style ( *. CSS* )
* JavaScript ( *. js* )
* Images ( *. png* , *. jpg* )

Par défaut, les fichiers statiques sont pris en charge uniquement à partir du répertoire racine Web et de ses sous-répertoires. Le chemin d’accès racine Web par défaut est *{racine du contenu}/wwwroot* . Spécifiez une autre racine Web en définissant son chemin d’accès lors de [la génération de l’hôte](#host). Pour plus d’informations, consultez [Racine web](xref:fundamentals/host/generic-host#webroot).

Empêcher la publication de fichiers dans *wwwroot* avec l' [ \<Content> élément de projet](/visualstudio/msbuild/common-msbuild-project-items#content) dans le fichier projet. L’exemple suivant empêche la publication de contenu dans *wwwroot/local* et dans ses sous-répertoires :

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Dans Razor les fichiers *. cshtml* , les barres obliques `~/` inverses () pointent vers la racine Web. Un chemin d’accès commençant par `~/` est désigné sous le terme de « *chemin d’accès virtuel* ».

Pour plus d'informations, consultez <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Cet article est une vue d’ensemble des sujets clés pour comprendre comment développer des applications ASP.NET Core.

## <a name="the-startup-class"></a>Classe Startup

La classe `Startup` est l’endroit où :

* Les services nécessaires à l’application sont configurés.
* Le pipeline de traitement des requêtes est défini.

Les *services* sont des composants utilisés par l’application. Par exemple, un composant de journalisation est un service. Le code pour configurer (ou *enregistrer* ) des services est ajouté à la méthode `Startup.ConfigureServices`.

Le pipeline de traitement des demandes est composé d’une série de composants d' *intergiciel (middleware* ). Par exemple, un intergiciel (middleware) peut gérer les demandes de fichiers statiques ou rediriger les requêtes HTTP vers HTTPS. Chaque intergiciel (middleware) effectue des opérations asynchrones sur `HttpContext`, puis appelle l’intergiciel (middleware) suivant dans le pipeline ou met fin à la requête. Le code pour configurer le pipeline de traitement des requêtes est ajouté à la méthode `Startup.Configure`.

Voici un exemple de classe `Startup` :

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Pour plus d'informations, consultez <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Injection de dépendances (services)

ASP.NET Core offre une infrastructure d’injection de dépendances (DI) intégrée qui rend disponibles les services configurés aux classes d’une application. Pour obtenir une instance d’un service dans une classe, vous pouvez créer un constructeur avec un paramètre du type requis. Le paramètre peut être le type de service ou une interface. Le système d’injection de dépendances fournit le service lors de l’exécution.

Voici une classe qui utilise l’injection de dépendances pour obtenir un objet de contexte Entity Framework Core. La ligne en surbrillance est un exemple d’injection de constructeur :

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

Si l’injection de dépendances est intégrée, elle est conçue pour vous permettre d’incorporer un conteneur d’inversion de contrôle (IoC) tiers si vous préférez.

Pour plus d'informations, consultez <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Le pipeline de traitement des requêtes est composé comme une série de composants d’intergiciel (middleware). Chaque composant effectue des opérations asynchrones sur `HttpContext`, puis appelle l’intergiciel (middleware) suivant dans le pipeline ou met fin à la requête.

Par convention, un composant d’intergiciel (middleware) est ajouté au pipeline en appelant sa méthode d’extension `Use...` dans la méthode `Startup.Configure`. Par exemple, pour activer le rendu des fichiers statiques, appelez `UseStaticFiles`.

Le code en surbrillance dans l’exemple suivant configure le pipeline de traitement des requêtes :

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core inclut un ensemble complet de middlewares intégrés, et vous pouvez écrire un middleware personnalisé.

Pour plus d'informations, consultez <xref:fundamentals/middleware/index>.

## <a name="host"></a>Hôte

Une application ASP.NET Core génère un *hôte* au démarrage. L’hôte est un objet qui encapsule toutes les ressources de l’application, telles que :

* Une implémentation serveur HTTP
* Composants d’intergiciel (middleware)
* Journalisation
* DI
* Configuration

La principale raison d’inclure toutes les ressources interdépendantes de l’application dans un objet est la gestion de la durée de vie : contrôler le démarrage de l’application et l’arrêt approprié.

Deux hôtes sont disponibles : l’hôte web et l’hôte générique. En ASP.NET Core 2.x, l’hôte générique est destiné uniquement aux scénarios non basés sur le web.

Le code permettant de créer un hôte se trouve dans `Program.Main` :

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

La méthode `CreateDefaultBuilder` permet de configurer un hôte avec les options fréquemment utilisées, notamment :

* Utilisez [Kestrel](#servers) en tant que serveur web et activez l’intégration IIS.
* Charger la configuration à partir de *appsettings.json* , *appSettings. { Nom de l’environnement}. JSON* , les variables d’environnement, les arguments de ligne de commande et d’autres sources de configuration.
* Envoyez la sortie de journalisation aux fournisseurs Console et Debug.

Pour plus d'informations, consultez <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Scénarios non basés sur le web

L’hôte générique permet à d’autres types d’application d’utiliser des extensions de framework composites, par exemple la journalisation, l’injection de dépendance, la configuration et la gestion de la durée de vie de l’application. Pour plus d’informations, consultez <xref:fundamentals/host/generic-host> et <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Serveurs

Une application ASP.NET Core utilise une implémentation de serveur HTTP pour écouter les requêtes HTTP. Les surfaces de serveur envoient des requêtes à l’application comme un ensemble de [fonctionnalités de requête](xref:fundamentals/request-features) composées dans un `HttpContext`.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core fournit les implémentations de serveur suivantes :

* *Kestrel* est un serveur web multiplateforme. Kestrel est souvent exécuté dans une configuration de proxy inverse à l’aide d’[IIS](https://www.iis.net/). Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet.
* *Le serveur HTTP IIS* est un serveur pour Windows qui utilise IIS. Avec ce serveur, l’application ASP.NET Core et IIS s’exécutent dans le même processus.
* *HTTP.sys* est un serveur Windows qui n’est pas utilisé avec IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel* . Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel* . Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core fournit les implémentations de serveur suivantes :

* *Kestrel* est un serveur web multiplateforme. Kestrel est souvent exécuté dans une configuration de proxy inverse à l’aide d’[IIS](https://www.iis.net/). Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet.
* *HTTP.sys* est un serveur Windows qui n’est pas utilisé avec IIS.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel* . Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core fournit l’implémentation du serveur multiplateforme *Kestrel* . Kestrel peut être exécuté en tant que serveur Edge public exposé directement à Internet. Kestrel est souvent exécuté dans une configuration de proxy inverse avec [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/).

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Pour plus d'informations, consultez <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Configuration

ASP.NET Core fournit une infrastructure de configuration qui obtient des paramètres en tant que paires nom-valeur à partir d’un ensemble ordonné de fournisseurs de configuration. Il existe des fournisseurs de configuration intégrés pour une grande variété de sources, tels que des fichiers *.json* , *.xml* , des variables d’environnement et des arguments de ligne de commande. Vous pouvez également écrire des fournisseurs de configuration.

Par exemple, vous pouvez spécifier que la configuration provient des *appsettings.json* variables d’environnement et. Ensuite, lorsque la valeur de *ConnectionString* est demandée, l’infrastructure recherche d’abord dans le *appsettings.json* fichier. Si la valeur est trouvée dans ce fichier, mais également dans une variable d’environnement, la valeur de la variable d’environnement est prioritaire.

Pour gérer des données de configuration confidentielles telles que les mots de passe, ASP.NET Core fournit un [outil Secret Manager](xref:security/app-secrets). Pour les secrets de production, nous vous recommandons [Azure Key Vault](xref:security/key-vault-configuration).

Pour plus d'informations, consultez <xref:fundamentals/configuration/index>.

## <a name="options"></a>Options

Lorsque cela est possible, ASP.NET Core suit le *modèle d’options* pour stocker et récupérer des valeurs de configuration. Le modèle d’options utilise des classes pour représenter les groupes de paramètres associés.

Par exemple, le code suivant définit des options WebSockets :

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Pour plus d'informations, consultez <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Environnements

Les environnements d’exécution, tels que *Développement* , *Mise en lots* et *Production* , sont une notion de premier plan dans ASP.NET Core. Vous pouvez spécifier l’environnement d’exécution d’une application en définissant la variable d’environnement `ASPNETCORE_ENVIRONMENT`. ASP.NET Core lit la variable d’environnement au démarrage de l’application et stocke la valeur dans une implémentation `IHostingEnvironment`. L’objet d’environnement est disponible partout dans l’application par le biais de l’injection de dépendances.

L’exemple de code suivant de la classe `Startup` configure l’application pour fournir des informations d’erreur détaillées uniquement lorsqu’elle s’exécute en développement :

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Pour plus d'informations, consultez <xref:fundamentals/environments>.

## <a name="logging"></a>Journalisation

ASP.NET Core prend en charge une API de journalisation qui fonctionne avec un large éventail de fournisseurs de journalisation intégrés et tiers. Les fournisseurs disponibles sont les suivants :

* Console
* Débogage
* Suivi des événements sur Windows
* Journal des événements Windows
* TraceSource
* Azure App Service
* Azure Application Insights

Écrivez des journaux à partir de n’importe quel emplacement dans le code d’une application en obtenant un objet `ILogger` à partir de l’injection de dépendances et en appelant les méthodes de journal.

Voici un exemple de code qui utilise un objet `ILogger`, avec l’injection de constructeur et les appels de méthode de journalisation mis en surbrillance.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

L’interface `ILogger` vous permet de passer un certain nombre de champs au fournisseur de journalisation. Les champs sont couramment utilisés pour construire une chaîne de message, mais le fournisseur peut également les envoyer en tant que champs distincts dans un magasin de données. Cette fonctionnalité permet aux fournisseurs de journalisation d’implémenter la [journalisation sémantique, également appelée journalisation structurée](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Pour plus d'informations, consultez <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routage

Un *itinéraire* est un modèle d’URL qui est mappé à un gestionnaire. Le gestionnaire est généralement une Razor page, une méthode d’action dans un contrôleur MVC ou un intergiciel (middleware). Le routage ASP.NET Core vous permet de contrôler les URL utilisées par votre application.

Pour plus d'informations, consultez <xref:fundamentals/routing>.

## <a name="error-handling"></a>Gestion des erreurs

ASP.NET Core offre des fonctionnalités intégrées pour gérer des erreurs, telles que :

* Une page d’exceptions du développeur
* Pages d’erreur personnalisées
* Pages de codes d’état statique
* Gestion des exceptions de démarrage

Pour plus d'informations, consultez <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Effectuer des requêtes HTTP

Une implémentation de `IHttpClientFactory` est disponible pour la création d’instances `HttpClient`. La fabrique :

* Fournit un emplacement central pour le nommage et la configuration d’instance de `HttpClient` logiques. Par exemple, un client *GitHub* peut être inscrit et configuré pour accéder à github. Un client par défaut peut être inscrit à d’autres fins.
* Prend en charge l’inscription et le chaînage de plusieurs gestionnaires de délégation pour créer un pipeline de middlewares pour les requêtes sortantes. Ce modèle est similaire au pipeline de middlewares entrants dans ASP.NET Core. Le modèle fournit un mécanisme permettant de gérer les problèmes transversaux liés aux des requêtes HTTP, notamment la mise en cache, la gestion des erreurs, la sérialisation et la journalisation.
* S’intègre à *Polly* , une bibliothèque tierce populaire pour la gestion des erreurs temporaires.
* Gère le regroupement et la durée de vie des instances de `HttpClientHandler` sous-jacentes pour éviter les problèmes DNS courants qui se produisent lors de la gestion manuelle des durées de vie de `HttpClient`.
* Ajoute une expérience de journalisation configurable (via `ILogger`) pour toutes les requêtes envoyées via des clients créés par la fabrique.

Pour plus d'informations, consultez <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Racine de contenu

La racine du contenu est le chemin d’accès de base à :

* Exécutable hébergeant l’application ( *. exe* ).
* Assemblys compilés qui composent l’application ( *. dll* ).
* Fichiers de contenu sans code utilisés par l’application, tels que :
  * Razor fichiers ( *. cshtml* , *. Razor* )
  * Fichiers de configuration ( *. JSON* , *. xml* )
  * Fichiers de données ( *. db* )
* [Racine Web](#web-root), en général le dossier *wwwroot* publié.

Lors du développement :

* La racine du contenu est par défaut le répertoire racine du projet.
* Le répertoire racine du projet est utilisé pour créer le :
  * Chemin d’accès aux fichiers de contenu sans code de l’application dans le répertoire racine du projet.
  * [Racine Web](#web-root), généralement le dossier *wwwroot* dans le répertoire racine du projet.

Vous pouvez spécifier un autre chemin d’accès racine [de contenu lors de la génération de l’hôte](#host). Pour plus d'informations, consultez <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Racine web

La racine Web est le chemin de base des fichiers de ressources statiques, non-code et publics, tels que :

* Feuilles de style ( *. CSS* )
* JavaScript ( *. js* )
* Images ( *. png* , *. jpg* )

Les fichiers statiques sont pris en charge par défaut uniquement à partir du répertoire racine Web (et des sous-répertoires).

Le chemin d’accès racine Web a comme valeur par défaut *{content root}/wwwroot* , mais une autre racine Web peut être spécifiée lors de [la génération de l’hôte](#host). Pour plus d’informations, consultez [Racine web](xref:fundamentals/host/web-host#web-root).

Empêcher la publication de fichiers dans *wwwroot* avec l' [ \<Content> élément de projet](/visualstudio/msbuild/common-msbuild-project-items#content) dans le fichier projet. L’exemple suivant empêche la publication de contenu dans le répertoire et les sous-répertoires *wwwroot/local* :

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

Dans les Razor fichiers ( *. cshtml* ), le tilde-slash ( `~/` ) pointe vers la racine Web. Un chemin d’accès commençant par `~/` est désigné sous le terme de « *chemin d’accès virtuel* ».

Pour plus d'informations, consultez <xref:fundamentals/static-files>.

::: moniker-end
