---
title: Présentation d’ASP.NET Core
author: rick-anderson
description: Apprenez-en plus sur ASP.NET Core, une infrastructure multiplateforme, hautes performances et open source pour la création d’applications modernes, basées sur le Cloud et connectées à Internet.
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
uid: index
ms.openlocfilehash: 3e41336d084e25319f8b1ab4c4ab3175b758d23d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588800"
---
# <a name="introduction-to-aspnet-core"></a>Présentation d’ASP.NET Core

Par [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Shaun Luttin](https://twitter.com/dicshaunary)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core est une infrastructure multiplateforme, à hautes performances et [Open source](https://github.com/dotnet/aspnetcore) pour la création d’applications modernes, basées sur le Cloud et connectées à Internet. Avec ASP.NET Core, vous pouvez :

* Créez des applications et services Web, des applications d' [Internet des objets (IOT)](https://www.microsoft.com/internet-of-things/) et des serveurs principaux mobiles.
* Utiliser vos outils de développement préférés sur Windows, macOS et Linux.
* Déployer dans le cloud ou localement.
* Exécutez sur [.net Core](/dotnet/core/introduction).

## <a name="why-choose-aspnet-core"></a>Pourquoi utiliser ASP.NET Core ?

Des millions de développeurs utilisent ou ont utilisé [ASP.net 4. x](/aspnet/overview) pour créer des applications Web. ASP.NET Core est une nouvelle conception de ASP.NET 4. x, y compris des modifications architecturales qui se traduisent par un Framework plus léger et plus modulaire.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Créer des API web et une interface utilisateur web en utilisant le modèle MVC d’ASP.NET Core

Le modèle MVC d’ASP.NET Core fournit des fonctionnalités pour créer des [API web](xref:tutorials/first-web-api) et des [applications web](xref:tutorials/razor-pages/index) :

* Le [modèle MVC (Modèle-Vue-Contrôleur)](xref:mvc/overview) permet de rendre vos API web et vos applications web testables.
* [ Razor Pages](xref:razor-pages/index) est un modèle de programmation basé sur des pages qui rend la création d’une interface utilisateur Web plus simple et plus productive.
* le [ Razor balisage](xref:mvc/views/razor) fournit une syntaxe productive pour les [ Razor pages](xref:razor-pages/index) et les [vues MVC](xref:mvc/views/overview).
* Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers.
* La prise en charge intégrée de [plusieurs formats de données et de la négociation de contenu](xref:web-api/advanced/formatting) permet à vos API web d’être utilisées par un large éventail de clients, notamment des navigateurs et des appareils mobiles.
* Le principe de la [liaison de modèle](xref:mvc/models/model-binding) permet le mappage automatiquement des données des requêtes HTTP aux paramètres des méthodes d’action.
* La [validation de modèle](xref:mvc/models/validation) effectue automatiquement la validation côté client et côté serveur.

## <a name="client-side-development"></a>Développement côté client

ASP.NET Core s’intègre de façon transparente aux infrastructures et bibliothèques courantes côté client, notamment [Blazor](xref:blazor/index) , [angulaires](xref:spa/angular), [réactifs](xref:spa/react)et [bootstrap](https://getbootstrap.com/). Pour plus d’informations, consultez <xref:blazor/index> et les rubriques connexes sous *Développement côté client*.

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a>ASP.NET Core frameworks cibles

ASP.NET Core 3. x et versions ultérieures peuvent uniquement cibler .NET Core. En règle générale, ASP.NET Core se compose de bibliothèques [.NET standard](/dotnet/standard/net-standard) . Les bibliothèques écrites avec .NET Standard 2.0 s’exécutent sur [n’importe quelle plateforme .NET qui implémente .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

Le ciblage de .NET Core présente plusieurs avantages, qui sont plus nombreux à chaque version. Voici certains avantages de .NET Core par rapport à .NET Framework :

* Multiplateforme. S’exécute sur Windows, macOS et Linux.
* performances améliorées
* [Contrôle de version côte à côte](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Nouvelles API
* Open source

## <a name="recommended-learning-path"></a>Parcours d’apprentissage recommandé

Pour une introduction au développement d’applications ASP.NET Core, nous vous recommandons d’utiliser la séquence de didacticiels suivante :

1. Suivez un didacticiel pour le type d’application que vous souhaitez développer ou gérer.

   |Type d’application  |Scénario  |Didacticiel  |
   |----------|----------|----------|
   |Application web                   | Nouveau développement de l’interface utilisateur Web côté serveur |[Prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) |
   |Application web                   | Maintenance d’une application MVC |[Bien démarrer avec MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |Application web                   | Développement de l’interface utilisateur Web côté client |[Prise en main de Blazor](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |API Web                   | Services HTTP RESTful |[Créer une API Web](xref:tutorials/first-web-api)&dagger; |
   |Application d’appel de procédure distante | Services contrat en premier à l’aide de mémoires tampons de protocole |[Bien démarrer avec un service gRPC](xref:tutorials/grpc/grpc-start) |
   |Application en temps réel             | Communication bidirectionnelle entre les serveurs et les clients connectés |[Prise en main de SignalR](xref:tutorials/signalr) |

1. Suivez un didacticiel qui montre comment effectuer un accès aux données de base.

   |Scénario  |Didacticiel  |
   |----------|----------|
   |Nouveau développement        |[Razor Pages avec Entity Framework Core](xref:data/ef-rp/intro) |
   |Maintenance d’une application MVC |[MVC avec Entity Framework Core](xref:data/ef-mvc/intro) |

1. Lisez une vue d’ensemble de ASP.NET Core [notions de base](xref:fundamentals/index) qui s’appliquent à tous les types d’applications.

1. Parcourez la table des matières pour consulter d’autres rubriques qui vous intéressent.

&dagger;Il y a également un [Didacticiel interactif sur l’API Web](/learn/modules/build-web-api-net-core). Aucune installation locale des outils de développement n’est requise. Le code s’exécute dans un [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) dans votre navigateur, et la [boucle](https://curl.haxx.se/) est utilisée pour le test.

## <a name="migrate-from-net-framework"></a>Migrer à partir de .NET Framework

Pour obtenir un guide de référence sur la migration d’applications ASP.NET 4. x vers ASP.NET Core, consultez <xref:migration/proper-to-2x/index> .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core est une infrastructure multiplateforme, à hautes performances et [Open source](https://github.com/dotnet/aspnetcore) pour la création d’applications modernes, basées sur le Cloud et connectées à Internet. Avec ASP.NET Core, vous pouvez :

* Créez des applications et services Web, des applications d' [Internet des objets (IOT)](https://www.microsoft.com/internet-of-things/) et des serveurs principaux mobiles.
* Utiliser vos outils de développement préférés sur Windows, macOS et Linux.
* Déployer dans le cloud ou localement.
* Exécuter sur [.NET Core ou .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).

## <a name="why-choose-aspnet-core"></a>Pourquoi utiliser ASP.NET Core ?

Des millions de développeurs utilisent ou ont utilisé [ASP.net 4. x](/aspnet/overview) pour créer des applications Web. ASP.NET Core est une refonte d’ASP.NET 4.x, avec des modifications d’architecture qui aboutissent à un framework plus léger et modulaire.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a>Créer des API web et une interface utilisateur web en utilisant le modèle MVC d’ASP.NET Core

Le modèle MVC d’ASP.NET Core fournit des fonctionnalités pour créer des [API web](xref:tutorials/first-web-api) et des [applications web](xref:tutorials/razor-pages/index) :

* Le [modèle MVC (Modèle-Vue-Contrôleur)](xref:mvc/overview) permet de rendre vos API web et vos applications web testables.
* [ Razor Pages](xref:razor-pages/index) est un modèle de programmation basé sur des pages qui rend la création d’une interface utilisateur Web plus simple et plus productive.
* le [ Razor balisage](xref:mvc/views/razor) fournit une syntaxe productive pour les [ Razor pages](xref:razor-pages/index) et les [vues MVC](xref:mvc/views/overview).
* Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers.
* La prise en charge intégrée de [plusieurs formats de données et de la négociation de contenu](xref:web-api/advanced/formatting) permet à vos API web d’être utilisées par un large éventail de clients, notamment des navigateurs et des appareils mobiles.
* Le principe de la [liaison de modèle](xref:mvc/models/model-binding) permet le mappage automatiquement des données des requêtes HTTP aux paramètres des méthodes d’action.
* La [validation de modèle](xref:mvc/models/validation) effectue automatiquement la validation côté client et côté serveur.

## <a name="client-side-development"></a>Développement côté client

ASP.NET Core s’intègre de façon transparente aux infrastructures et bibliothèques courantes côté client, notamment [Blazor](xref:blazor/index) , [angulaires](xref:spa/angular), [réactifs](xref:spa/react)et [bootstrap](https://getbootstrap.com/). Pour plus d’informations, consultez <xref:blazor/index> et les rubriques connexes sous *Développement côté client*.

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a>ASP.NET Core ciblant .NET Framework

ASP.NET Core 2.x peut cibler .NET Core ou le .NET Framework. Les applications ASP.NET Core ciblant .NET Framework ne sont pas multiplateformes : elles s’exécutent seulement sur Windows. D’une façon générale, ASP.NET Core 2.x est constitué de bibliothèques [.NET Standard](/dotnet/standard/net-standard). Les bibliothèques écrites avec .NET Standard 2.0 s’exécutent sur [n’importe quelle plateforme .NET qui implémente .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).

ASP.NET Core 2.x est pris en charge sur les versions .NET Framework compatibles avec .NET Standard 2.0 :

* .NET Framework dernière version est recommandée.
* .NET Framework 4.6.1 et versions ultérieures.

ASP.NET Core 3.0 et ultérieur s’exécute uniquement sur .NET Core. Pour plus de détails concernant ce changement, consultez [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

Le ciblage de .NET Core présente plusieurs avantages, qui sont plus nombreux à chaque version. Voici certains avantages de .NET Core par rapport à .NET Framework :

* Multiplateforme. S’exécute sur macOS, Linux et Windows
* performances améliorées
* [Contrôle de version côte à côte](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* Nouvelles API
* Open source

Pour aider à fermer le fossé entre les API .NET Framework et .NET Core, le [Pack de compatibilité Windows](/dotnet/core/porting/windows-compat-pack) a fait des milliers d’API Windows uniquement disponibles dans .net core. Ces API n’étaient pas disponibles dans .NET Core 1.x.

## <a name="recommended-learning-path"></a>Parcours d’apprentissage recommandé

Nous vous recommandons la séquence de tutoriels et d’articles suivante comme introduction au développement des applications ASP.NET Core :

1. Suivez un didacticiel pour le type d’application que vous souhaitez développer ou gérer.

   |Type d’application  |Scénario  |Didacticiel  |
   |----------|----------|----------|
   |Application web                   | Pour un nouveau développement        |[Prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) |
   |Application web                   | Pour maintenir une application MVC |[Bien démarrer avec MVC](xref:tutorials/first-mvc-app/start-mvc)|
   |API Web                   |                            |[Créer une API Web](xref:tutorials/first-web-api)&dagger; |
   |Application en temps réel             |                            |[Prise en main de SignalR](xref:tutorials/signalr) |

1. Suivez un didacticiel qui montre comment effectuer un accès aux données de base.

   |Scénario  |Didacticiel  |
   |----------|----------|
   | Pour un nouveau développement        |[Razor Pages avec Entity Framework Core](xref:data/ef-rp/intro) |
   | Pour maintenir une application MVC |[MVC avec Entity Framework Core](xref:data/ef-mvc/intro) |

1. Lisez une vue d’ensemble de ASP.NET Core [notions de base](xref:fundamentals/index) qui s’appliquent à tous les types d’applications.

1. Parcourez la Table des matières pour d’autres rubriques qui vous intéressent.

&dagger;Il y a également un [didacticiel d’API Web que vous suivez entièrement dans le navigateur](/learn/modules/build-web-api-net-core), aucune installation IDE locale n’est requise. Le code s’exécute dans [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), et [curl](https://curl.haxx.se/) est utilisé à des fins de test.

## <a name="migrate-from-net-framework"></a>Migrer à partir de .NET Framework

Pour obtenir un guide de référence sur la migration d’applications ASP.NET vers ASP.NET Core, consultez <xref:migration/proper-to-2x/index> .

::: moniker-end

## <a name="how-to-download-a-sample"></a>Comment télécharger un exemple

La plupart des articles et tutoriels contiennent des liens vers des exemples de code.

1. [Téléchargez le fichier zip du référentiel ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/main).
1. Décompressez le fichier *Docs-main.zip* .
1. Utilisez l’URL contenue dans l’exemple de lien pour vous aider à naviguer dans l’exemple de répertoire.

### <a name="preprocessor-directives-in-sample-code"></a>Directives de préprocesseur dans l’exemple de code

Pour illustrer plusieurs scénarios, les exemples d’applications utilisent les `#define` `#if-#else/#elif-#endif` directives de préprocesseur et pour compiler et exécuter de manière sélective différentes sections d’exemples de code. Pour les exemples qui utilisent cette approche, définissez la `#define` directive en haut des fichiers C# pour définir le symbole associé au scénario que vous souhaitez exécuter. Certains exemples requièrent la définition du symbole en haut de plusieurs fichiers afin d’exécuter un scénario.

Par exemple, la liste des symboles `#define` suivante indique que les quatre scénarios sont disponibles (un scénario par symbole). La configuration actuelle de l’exemple exécute le scénario `TemplateCode` :

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

Pour que l’exemple exécute le scénario `ExpandDefault`, définissez le symbole `ExpandDefault` et laissez les symboles restants commentés :

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

Pour plus d’informations sur l’utilisation des [directives de préprocesseur C#](/dotnet/csharp/language-reference/preprocessor-directives/) pour compiler de façon sélective des sections de code, consultez [#define (Référence C#)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) et [#if (Référence C#) ](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).

### <a name="regions-in-sample-code"></a>Régions dans l’exemple de code

Certains exemples d’applications contiennent des sections de code entourées de [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) et [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) directives C#. Le système de génération de documentation injecte ces régions dans les rubriques de documentation affichées.  

Les noms des régions contiennent généralement le mot « snippet ». L’exemple suivant montre une région nommée `snippet_WebHostDefaults` :

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

L’extrait de code C# précédent est référencé dans le fichier Markdown de la rubrique avec la ligne suivante :

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

Vous pouvez ignorer (ou supprimer) en toute sécurité `#region` les `#endregion` directives et qui entourent le code. Ne modifiez pas le code dans ces directives si vous envisagez d’exécuter les exemples de scénarios décrits dans la rubrique. N’hésitez pas à modifier le code quand vous testez d’autres scénarios.

Pour plus d’informations, consultez [Contribuer à la documentation ASP.NET : extraits de code](https://github.com/dotnet/AspNetCore.Docs/blob/main/CONTRIBUTING.md#code-snippets).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Notions de base d’ASP.NET Core](xref:fundamentals/index)
* [Le point hebdomadaire de la communauté ASP.NET](https://live.asp.net/) couvre l’avancement et les plans des équipes de développement. Il met en avant de nouveaux blogs et des logiciels de tiers.
