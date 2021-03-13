---
title: Migrer d’ASP.NET vers ASP.NET Core
author: isaac2004
description: Recevoir des conseils de migration d’applications ASP.NET MVC ou Web API existantes vers ASP.NET Core.web
ms.author: scaddie
ms.date: 10/18/2019
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
uid: migration/proper-to-2x/index
ms.openlocfilehash: 7961890becc8f4513e0750f28341c9d4cf94e7ad
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413330"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>Migrer d’ASP.NET vers ASP.NET Core

De [Isaac Levin](https://isaaclevin.com)

Cet article sert de guide de référence pour la migration d’applications ASP.NET vers ASP.NET Core. Pour obtenir un guide de Portage complet, consultez le livre électronique [Portage d’applications ASP.NET existantes vers .net Core](https://aka.ms/aspnet-porting-ebook) .

## <a name="prerequisites"></a>Prérequis

[Kit SDK .NET Core 2,2 ou version ultérieure](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a>Versions cibles de .NET Framework

Les projets ASP.NET Core permettent aux développeurs de cibler .NET Core, le .NET Framework ou les deux. Consultez [Choix entre le .NET Core et le .NET Framework pour les applications serveur](/dotnet/standard/choosing-core-framework-server) afin de déterminer quel est le framework cible le plus approprié.

Quand vous ciblez le .NET Framework, les projets doivent référencer des packages NuGet individuels.

Le ciblage du .NET Core vous permet d’éliminer de nombreuses références de packages explicites, grâce au [métapackage](xref:fundamentals/metapackage-app) ASP.NET Core. Installez le métapackage `Microsoft.AspNetCore.App` dans votre projet :

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

Quand le métapackage est utilisé, aucun package référencé dans le métapackage n’est déployé avec l’application. Le magasin de runtimes du .NET Core inclut ces composants. Ceux-ci sont précompilés pour améliorer les performances. Pour plus d’informations, consultez [Métapackage Microsoft.AspNetCore.App pour ASP.NET Core](xref:fundamentals/metapackage-app).

## <a name="project-structure-differences"></a>Différences de structure de projet

Le format de fichier *.csproj* a été simplifié dans ASP.NET Core. Voici certains changements notables :

- Il n’est pas nécessaire d’inclure explicitement les fichiers pour qu’ils soient considérés comme faisant partie du projet. Cela réduit le risque de conflits de fusion XML quand vous travaillez avec des équipes de grande taille.
- Il n’existe aucune référence basée sur un GUID à d’autres projets, ce qui améliore la lisibilité du fichier.
- Vous pouvez modifier le fichier sans devoir le décharger dans Visual Studio :

    ![Option de menu contextuel de modification du CSPROJ dans Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Remplacement du fichier Global.asax

ASP.NET Core a introduit un nouveau mécanisme pour le démarrage d’une application. Le point d’entrée des applications ASP.NET est le fichier *Global.asax*. Les tâches telles que la configuration de l’itinéraire ou l’inscription des filtres et des zones sont traitées dans le fichier *Global.asax*.

[!code-csharp[](samples/globalasax-sample.cs)]

Cette approche couple l’application au serveur sur lequel elle est déployée d’une manière qui interfère avec l’implémentation. Pour y remédier, [OWIN](https://owin.org/) a donc été introduit afin d’optimiser l’utilisation de plusieurs frameworks à la fois. OWIN fournit un pipeline qui permet d’ajouter uniquement les modules nécessaires. L’environnement d’hébergement utilise une fonction [Startup](xref:fundamentals/startup) pour configurer les services et le pipeline de requêtes de l’application. `Startup` inscrit un ensemble d’intergiciels (middleware) auprès de l’application. Pour chaque requête, l’application appelle chacun des composants intergiciels (middleware) à l’aide du pointeur d’en-tête d’une liste liée à un ensemble existant de gestionnaires. Chaque composant intergiciel (middleware) peut ajouter un ou plusieurs gestionnaires au pipeline de traitement des requêtes. Pour ce faire, une référence doit être retournée au gestionnaire qui représente le nouvel en-tête de la liste. Chaque gestionnaire doit mémoriser et appeler le prochain gestionnaire de la liste. Avec ASP.NET Core, comme le point d’entrée d’une application est `Startup`, vous n’avez plus de dépendance relative à *Global.asax*. Quand vous employez OWIN avec le .NET Framework, utilisez l’exemple de code suivant en tant que pipeline :

[!code-csharp[](samples/webapi-owin.cs)]

Cela permet de configurer vos itinéraires par défaut, et de privilégier XmlSerialization à JSON. Ajoutez d’autres intergiciels (middleware) à ce pipeline selon les besoins (services de chargement, paramètres de configuration, fichiers statiques, etc.).

ASP.NET Core utilise une approche similaire mais n’a pas besoin d’OWIN pour prendre en charge l’entrée. Au lieu de cela, cette opération est effectuée via la méthode *Program.cs* `Main` (semblable aux applications console) et `Startup` est chargée par l’intermédiaire de là.

[!code-csharp[](samples/program.cs)]

`Startup` doit inclure une méthode `Configure`. Dans `Configure`, ajoutez l’intergiciel (middleware) nécessaire au pipeline. Dans l’exemple suivant (provenant du modèle de site web par défaut), des méthodes d’extension configurent le pipeline pour une prise en charge des éléments ci-dessous :

- Pages d’erreur
- HTTP Strict Transport Security
- Redirection HTTP vers HTTPS
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

L’hôte et l’application ont été découplés, ce qui permet de passer facilement plus tard à une autre plateforme.

> [!NOTE]
> Pour obtenir des informations de référence plus approfondies sur le démarrage des applications dans ASP.NET Core et sur les intergiciels (middleware), consultez [Démarrage dans ASP.NET Core](xref:fundamentals/startup)

## <a name="store-configurations"></a>Stocker les configurations

ASP.NET prend en charge le stockage des paramètres. Ces paramètres sont utilisés, par exemple, pour prendre en charge l’environnement sur lequel les applications ont été déployées. Habituellement, toutes les paires clé-valeur personnalisées sont stockées dans la section `<appSettings>` du fichier *Web.config* :

[!code-xml[](samples/webconfig-sample.xml)]

Les applications lisent ces paramètres à l’aide de la collection `ConfigurationManager.AppSettings` dans l’espace de noms `System.Configuration` :

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core peut stocker les données de configuration de l’application dans un fichier et les charger dans le cadre du démarrage d’un intergiciel (middleware). Le fichier par défaut utilisé dans les modèles de projet est le *appsettings.json* suivant :

[!code-json[](samples/appsettings-sample.json)]

Le chargement de ce fichier dans une instance de `IConfiguration` au sein de votre application s’effectue dans *Startup.cs* :

[!code-csharp[](samples/startup-builder.cs)]

L’application lit `Configuration` pour obtenir les paramètres :

[!code-csharp[](samples/read-appsettings.cs)]

Il existe des extensions à cette approche pour rendre le processus plus robuste, par exemple en utilisant l’[injection de dépendances](xref:fundamentals/dependency-injection) pour charger un service avec ces valeurs. L’approche par injection de dépendances fournit un ensemble d’objets de configuration fortement typés.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> Pour obtenir des informations de référence plus approfondies sur la configuration d’ASP.NET Core, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index).

## <a name="native-dependency-injection"></a>Injection de dépendances native

Quand vous générez des applications majeures et scalables, il est important d’avoir un couplage faible entre les composants et les services. L’[injection de dépendances](xref:fundamentals/dependency-injection) est une technique répandue qui permet d’y parvenir. Elle représente un composant natif d’ASP.NET Core.

Dans les applications ASP.NET, les développeurs s’appuient sur une bibliothèque tierce pour implémenter l’injection de dépendances. L’une de ces bibliothèques, [Unity](https://github.com/unitycontainer/unity), est fournie par Microsoft Patterns & Practices.

L’implémentation de `IDependencyResolver` qui inclut `UnityContainer` dans un wrapper est un exemple de configuration d’une injection de dépendances avec Unity :

[!code-csharp[](samples/sample8.cs)]

Créez une instance de `UnityContainer`, inscrivez votre service, puis définissez le programme de résolution de dépendances de `HttpConfiguration` en fonction de la nouvelle instance de `UnityResolver` pour votre conteneur :

[!code-csharp[](samples/sample9.cs)]

Injectez `IProductRepository` aux emplacements nécessaires :

[!code-csharp[](samples/sample5.cs)]

Dans la mesure où l’injection de dépendances fait partie d’ASP.NET Core, vous pouvez ajouter votre service à la méthode `ConfigureServices` de *Startup.cs* :

[!code-csharp[](samples/configure-services.cs)]

Vous pouvez injecter le dépôt à l’emplacement de votre choix, comme c’était le cas avec Unity.

> [!NOTE]
> Pour plus d’informations sur l’injection de dépendances, consultez [Injection de dépendances](xref:fundamentals/dependency-injection).

## <a name="serve-static-files"></a>Délivrer des fichiers statiques

Une partie importante du développement web réside dans la capacité de traitement des composants statiques, côté client. Les fichiers HTML, CSS, JavaScript et image sont les exemples les plus courants de fichiers statiques. Ces fichiers doivent être enregistrés à l’emplacement publié de l’application (ou CDN) et référencés pour pouvoir être chargés par une requête. Ce processus a changé avec ASP.NET Core.

Avec ASP.NET, les fichiers statiques sont stockés dans différents répertoires et référencés dans des vues.

Dans ASP.NET Core, les fichiers statiques sont stockés dans la « racine Web » (*&lt; racine du contenu &gt; /wwwroot*), sauf si configuré dans le cas contraire. Les fichiers sont chargés dans le pipeline de requêtes via l’appel de la méthode d’extension `UseStaticFiles` à partir de `Startup.Configure` :

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> Si vous ciblez le .NET Framework, installez le package NuGet `Microsoft.AspNetCore.StaticFiles`.

Par exemple, un composant image dans le dossier *wwwroot/images* est accessible au navigateur à un emplacement tel que `http://<app>/images/<imageFileName>`.

> [!NOTE]
> Pour obtenir des informations de référence plus approfondies sur le traitement des fichiers statiques dans ASP.NET Core, consultez [Fichiers statiques](xref:fundamentals/static-files).

## <a name="multi-value-cookies"></a>cookieValeurs multiples

[Les valeurs à cookie valeurs multiples](xref:System.Web.HttpCookie.Values) ne sont pas prises en charge dans ASP.net core. Créez un cookie par valeur.

## <a name="authentication-cookies-are-not-compressed-in-aspnet-core"></a>Les s d’authentification ne cookie sont pas compressées dans ASP.net Core

[!INCLUDE[](~/includes/cookies-not-compressed.md)]

## <a name="partial-app-migration"></a>Migration d’application partielle

L’une des approches de la migration partielle d’applications consiste à créer une sous-application IIS et à déplacer uniquement certains itinéraires de ASP.NET 4. x vers ASP.NET Core tout en conservant la structure de l’URL de l’application. Par exemple, considérez la structure de l’URL de l’application à partir du fichier *applicationHost.config* :

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

Structure de répertoire :

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="bind-and-input-formatters"></a>[BIND] et formateurs d’entrée

Les [versions précédentes de ASP.net](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) utilisaient l' `[Bind]` attribut pour vous protéger contre les attaques de survalidation. Les [formateurs d’entrée](xref:mvc/models/model-binding#input-formatters) fonctionnent différemment dans ASP.net core. L' `[Bind]` attribut n’est plus conçu pour empêcher la survalidation lorsqu’il est utilisé avec des formateurs d’entrée pour analyser JSON ou XML. Ces attributs affectent la liaison de modèle lorsque la source de données est une donnée de formulaire publiée avec le `x-www-form-urlencoded` type de contenu.

Pour les applications qui publient des informations JSON sur les contrôleurs et utilisent des formateurs d’entrée JSON pour analyser les données, nous vous recommandons de remplacer l' `[Bind]` attribut par un modèle de vue qui correspond aux propriétés définies par l' `[Bind]` attribut.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Portage des bibliothèques vers le .NET Core](/dotnet/core/porting/libraries)
