---
title: ASP.NET Core et Entity Framework 6
author: rick-anderson
description: Entity Framework 6,3 et versions ultérieures fonctionnent avec ASP.NET Core 3,1 et versions ultérieures.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 44211ac7fa2acc7a7a9471ef362cff02f94fa2b6
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588267"
---
# <a name="aspnet-core-and-entity-framework-6"></a>ASP.NET Core et Entity Framework 6
::: moniker range=">= aspnetcore-3.0"

De [Patrick Goode](https://github.com/attrib75)

## <a name="using-entity-framework-6-with-aspnet-core"></a>Utilisation de Entity Framework 6 avec ASP.NET Core

[Entity Framework Core](/ef/) doit être utilisé pour un nouveau développement. L' [exemple Download](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/3.xsample) utilise [Entity Framework 6 (EF6)](/ef/ef6), qui peut être utilisé pour migrer les applications de sortie vers ASP.net core.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Entity Framework - Configuration basée sur le code](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Par [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) et [Tom Dykstra](https://github.com/tdykstra)

Cet article montre comment utiliser Entity Framework 6 dans une application ASP.NET Core.    

## <a name="overview"></a>Vue d’ensemble 

Pour utiliser Entity Framework 6, votre projet doit être compilé pour .NET Framework, car Entity Framework 6 ne prend pas en charge .NET Core. Si vous avez besoin de fonctionnalités multiplateformes, vous devez effectuer une mise à niveau vers [Entity Framework Core](/ef/).  

La méthode recommandée pour utiliser Entity Framework 6 dans une application ASP.NET Core consiste à placer le contexte EF6 et les classes de modèle dans un projet de bibliothèque de classes qui cible .NET Framework. Ajoutez une référence à la bibliothèque de classes depuis le projet ASP.NET Core. Consultez l’exemple [Solution Visual Studio avec des projets Entity Framework 6 et ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/).    

Vous ne pouvez pas mettre un contexte EF6 dans un projet ASP.NET Core, car les projets .NET Core ne prennent pas en charge toutes les fonctionnalités nécessaires pour les commandes EF6, comme *Enable-Migrations*.    

Quel que soit le type de projet où vous placez votre contexte EF6, seuls les outils en ligne de commande EF6 fonctionnent avec un contexte EF6. Par exemple, `Scaffold-DbContext` est disponible seulement dans Entity Framework Core. Si vous devez effectuer une ingénierie à rebours d’une base de données dans un modèle EF6, consultez <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database> .    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Référencer le framework complet et EF6 dans le projet ASP.NET Core 

Votre projet de ASP.NET Core doit cibler .NET Framework et référencer EF6. Par exemple, le fichier *.csproj* de votre projet ASP.NET Core doit être similaire à l’exemple suivant (seules les parties concernées du fichier sont montrées).    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

Quand vous créez un projet, utilisez le modèle **Application web ASP.NET Core (.NET Framework)**.    

## <a name="handle-connection-strings"></a>Gérer les chaînes de connexion    

Les outils en ligne de commande EF6 que vous allez utiliser dans le projet de bibliothèque de classes EF6 nécessitent un constructeur par défaut pour pouvoir instancier le contexte. Si vous voulez spécifier la chaîne de connexion à utiliser dans le projet ASP.NET Core, votre constructeur de contexte doit avoir un paramètre qui vous permet de passer la chaîne de connexion. Voici un exemple.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

Étant donné que votre contexte EF6 n’a pas de constructeur sans paramètre, votre projet EF6 doit fournir une implémentation de <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0> . Les outils en ligne de commande EF6 trouvent et utilisent cette implémentation : ils peuvent donc instancier le contexte. Voici un exemple.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

Dans cet exemple de code, l’implémentation de `IDbContextFactory` passe une chaîne de connexion codée en dur. Il s’agit de la chaîne de connexion utilisée par les outils en ligne de commande. Vous pouvez implémenter une stratégie pour garantir que la bibliothèque de classes utilise la même chaîne de connexion que celle de l’application appelante. Par exemple, vous pouvez obtenir la valeur d’une variable d’environnement dans les deux projets.   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Configurer l’injection de dépendances dans le projet ASP.NET Core  

Dans le fichier *Startup.cs* du projet Core, configurez le contexte EF6 pour l’injection de dépendances dans `ConfigureServices`. Les objets de contexte EF doit avoir une durée de vie limitée à une demande.   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

Vous pouvez ensuite obtenir une instance du contexte dans vos contrôleurs en utilisant l’injection de dépendances. Le code est similaire à ce que vous pourriez écrire pour un contexte EF Core :    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a>Exemple d’application   

Pour un exemple d’application opérationnelle, consultez [l’exemple de solution Visual Studio](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/) qui accompagne cet article.    

Vous pouvez créer cet exemple à partir de zéro dans Visual Studio en effectuant les étapes suivantes :    

* Créez une solution.    

* **Ajouter** > **Nouveau projet** > **Web** > **Application web ASP.NET Core**    
  * Dans la boîte de dialogue de sélection du modèle de projet, sélectionnez API et .NET Framework dans la liste déroulante 

* **Ajouter** > **Nouveau projet** > **Windows Desktop** > **Bibliothèque de classes (.NET Framework)**  

* Dans la **Console du Gestionnaire de package** pour les deux projets, exécutez la commande `Install-Package Entityframework`.    

* Dans le projet de bibliothèque de classes, créez des classes de modèle de données et une classe de contexte, ainsi qu’une implémentation de `IDbContextFactory`.    

* Dans la console du Gestionnaire de package, pour le projet de bibliothèque de classes, exécutez les commandes `Enable-Migrations` et `Add-Migration Initial`. Si vous avez défini le projet ASP.NET Core comme projet de démarrage, ajoutez `-StartupProjectName EF6` à ces commandes. 

* Dans le projet Core, ajoutez une référence de projet au projet de bibliothèque de classes.    

* Dans le projet Core, dans *Startup.cs*, inscrivez le contexte pour l’injection de dépendances.    

* Dans le projet de base, dans *appsettings.json* , ajoutez la chaîne de connexion.  

* Dans le projet Core, ajoutez un contrôleur et une ou plusieurs vues pour vérifier que vous pouvez lire et écrire des données. (Notez que la génération de modèles automatique d’ASP.NET Core MVC ne fonctionne pas avec le contexte EF6 référencé depuis la bibliothèque de classes.)

::: moniker-end
