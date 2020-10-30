---
title: Migrer d’ASP.NET Core 1.x vers la version 2.0
author: scottaddie
description: Cet article présente les prérequis et la plupart des étapes courantes nécessaires pour la migration d’un projet ASP.NET Core 1.x vers ASP.NET Core 2.0.
ms.author: scaddie
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
uid: migration/1x-to-2x/index
ms.openlocfilehash: 6d67924d87cdbe72cb08c5305dfe45c5b22b31bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057113"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>Migrer d’ASP.NET Core 1.x vers la version 2.0

Par [Scott Addie](https://github.com/scottaddie)

Dans cet article, nous vous montrons comment mettre à jour un projet ASP.NET Core 1.x existant vers ASP.NET Core 2.0. La migration de votre application vers ASP.NET Core 2.0 vous permet de tirer parti de [nombreuses nouvelles fonctionnalités et améliorations des performances](xref:aspnetcore-2.0).

Les applications ASP.NET Core 1.x existantes sont basées sur des modèles de projet propres à la version. Les modèles de projet et le code de démarrage qu’ils contiennent évoluent en même temps que le framework ASP.NET Core. Outre le framework ASP.NET Core, vous devez aussi mettre à jour le code de votre application.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Prérequis

Consultez [Bien démarrer avec ASP.NET Core](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Mettre à jour le Moniker du Framework cible

Les projets ciblant .NET Core doivent utiliser le [Moniker du Framework cible](/dotnet/standard/frameworks) d’une version supérieure ou égale à .NET Core 2.0. Recherchez le nœud `<TargetFramework>` dans le fichier *.csproj* et remplacez son texte interne par `netcoreapp2.0` :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

Les projets ciblant le .NET Framework doivent utiliser le Moniker du Framework cible d’une version supérieure ou égale à .NET Framework 4.6.1. Recherchez le nœud `<TargetFramework>` dans le fichier *.csproj* et remplacez son texte interne par `net461` :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET Core 2.0 offre une surface d’exposition beaucoup plus grande que .NET Core 1.x. Si vous ciblez le .NET Framework uniquement car il manque des API dans .NET Core 1.x, le ciblage de .NET Core 2.0 fonctionnera sans doute.

Si le fichier projet contient `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, voir [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>Mettre à jour la version du SDK .NET Core dans global.json

Si votre solution s’appuie sur un fichier [global.json](/dotnet/core/tools/global-json) pour cibler une version spécifique du SDK .NET Core, mettez à jour sa propriété `version` de façon à utiliser la version 2.0 installée sur votre ordinateur :

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Mettre à jour les références de package

Le fichier *.csproj* dans un projet 1.x répertorie chaque package NuGet utilisé par le projet.

Dans un projet ASP.NET Core 2.0 ciblant .NET Core 2.0, une seule référence de [métapackage](xref:fundamentals/metapackage) dans le fichier *.csproj* remplace la collection de packages :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

Toutes les fonctionnalités d’ASP.NET Core 2.0 et d’Entity Framework Core 2.0 sont incluses dans le métapackage.

Les projets ASP.NET Core 2.0 ciblant le .NET Framework doivent continuer à référencer des packages NuGet spécifiques. Mettez à jour l’attribut `Version` de chaque nœud `<PackageReference />` vers la version 2.0.0.

Par exemple, voici la liste des nœuds `<PackageReference />` utilisés dans un projet ASP.NET Core 2.0 standard qui cible le .NET Framework :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>Mettre à jour les outils CLI .NET Core

Dans le fichier *.csproj* , mettez à jour l’attribut `Version` de chaque nœud `<DotNetCliToolReference />` vers la version 2.0.0.

Par exemple, voici la liste des outils CLI utilisés dans un projet ASP.NET Core 2.0 standard qui cible .NET Core 2.0 :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Renommer la propriété PackageTargetFallback

Le fichier *.csproj* d’un projet 1.x utilise un nœud et une variable `PackageTargetFallback` :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Renommez le nœud et la variable `AssetTargetFallback` :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>Mettre à jour la méthode Main dans Program.cs

Dans les projets 1.x, la méthode `Main` de *Program.cs* ressemble à ceci :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

Dans les projets 2.0, la méthode `Main` de *Program.cs* a été simplifiée :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

L’adoption de ce nouveau modèle 2.0 est vivement recommandée, et même obligatoire pour bénéficier des fonctionnalités de produit telles que [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations). Par exemple, l’exécution de `Update-Database` à partir de la fenêtre de la Console du Gestionnaire de Package ou de `dotnet ef database update` à partir de la ligne de commande (sur des projets convertis vers ASP.NET Core 2.0) génère l’erreur suivante :

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Ajouter des fournisseurs de configuration

Dans les projets 1.x, l’ajout de fournisseurs de configuration à une application a été effectué via le constructeur `Startup`. Étapes nécessaires à la création d’une instance de `ConfigurationBuilder`, chargement des fournisseurs applicables (variables d’environnement, paramètres de l’application, etc.) et initialisation d’un membre de `IConfigurationRoot`.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

L’exemple précédent charge le `Configuration` membre avec les paramètres de configuration de *appsettings.json* et tout *appSettings. \<EnvironmentName\> fichier JSON* correspondant à la `IHostingEnvironment.EnvironmentName` propriété. L’emplacement de ces fichiers est sur le même chemin que *Startup.cs* .

Dans les projets 2.0, le code de configuration réutilisable inhérent aux projets 1.x s’exécute en arrière-plan. Par exemple, les variables d’environnement et les paramètres de l’application sont chargés au démarrage. Le code équivalent *Startup.cs* est réduit à l’initialisation de `IConfiguration` avec l’instance injectée :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Pour supprimer les fournisseurs par défaut ajoutés par `WebHostBuilder.CreateDefaultBuilder`, appelez la méthode `Clear` sur la propriété `IConfigurationBuilder.Sources` dans `ConfigureAppConfiguration`. Pour rajouter fournisseurs, utilisez la méthode `ConfigureAppConfiguration` dans *Program.cs*  :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

La configuration utilisée par la méthode `CreateDefaultBuilder` dans l’extrait de code précédent peut être consultée [ici](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).

Pour plus d’informations, consultez [Configuration dans ASP.NET Core](xref:fundamentals/configuration/index).

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Déplacer le code d’initialisation de base de données

Dans les projets 1.x utilisant EF Core 1.x, une commande telle que `dotnet ef migrations add` effectue les opérations suivantes :

1. Instancie une instance `Startup`
1. Appelle la méthode `ConfigureServices` pour inscrire tous les services à l’injection de dépendances (notamment les types `DbContext`)
1. Exécute ses tâches obligatoires

Dans les projets 2.0 utilisant EF Core 2.0, `Program.BuildWebHost` est appelé pour obtenir les services d’application. Contrairement à 1.x, cela a pour autre effet secondaire d’appeler `Startup.Configure`. Si votre application 1.x a appelé le code d’initialisation de base de données dans sa méthode `Configure`, des problèmes inattendus peuvent se produire. Par exemple, si la base de données n’existe pas encore, le code d’amorçage s’exécute avant l’exécution de la commande EF Core Migrations. Ce problème entraîne l’échec de la commande `dotnet ef migrations list` si la base de données n’existe pas encore.

Envisagez le code d’initialisation d’amorçage 1.x suivant dans la méthode `Configure` de *Startup.cs*  :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

Dans les projets 2.0, déplacez l’appel de `SeedData.Initialize` vers la méthode `Main` de *Program.cs*  :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

À compter de la version 2.0, il est déconseillé de faire quoi que ce soit dans `BuildWebHost`, sauf de créer et de configurer l’hôte web. Tout ce qui concerne l’exécution de l’application doit être géré en dehors de `BuildWebHost` &mdash; généralement dans la méthode `Main` de *Program.cs* .

<a name="view-compilation"></a>

## <a name="review-no-locrazor-view-compilation-setting"></a>Vérifier le paramètre de compilation de la Razor vue

La vitesse de démarrage d’application et la taille des bundles publiés ont une importance capitale pour vous. Pour ces raisons, la compilation de l' [ Razor affichage](xref:mvc/views/view-compilation) est activée par défaut dans ASP.net Core 2,0.

L’affectation de la valeur true à la propriété `MvcRazorCompileOnPublish` n’est plus nécessaire. Vous pouvez supprimer la propriété du fichier *.csproj* , sauf si vous désactivez la compilation des vues.

Quand vous ciblez .NET Framework, vous devez toujours faire référence explicitement à [Microsoft. AspNetCore. Mvc. Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) Package NuGet ViewCompilation dans votre fichier *. csproj* :

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>S’appuyer sur les fonctionnalités de mise en évidence d’Application Insights

La configuration sans effort de l’instrumentation des performances d’application est importante. Vous pouvez maintenant vous appuyer sur les nouvelles fonctionnalités de mise en évidence [d’Application Insights](/azure/application-insights/app-insights-overview) disponibles dans les outils Visual Studio 2017.

Par défaut, les projets ASP.NET Core 1.1 créés dans Visual Studio 2017 ajoutaient Application Insights. Si vous n’utilisez pas le SDK Application Insights directement, en dehors de *Program.cs* et *Startup.cs* , effectuez les étapes suivantes :

1. Si vous ciblez .NET Core, supprimez le nœud `<PackageReference />` suivant dans le fichier *.csproj* :

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. Si vous ciblez .NET Core, supprimez l’appel de méthode d’extension `UseApplicationInsights` de *Program.cs* :

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. Supprimez l’appel d’API côté client Application Insights du fichier *_Layout.cshtml* . Il comprend les deux lignes de code suivantes :

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Si vous utilisez le SDK Application Insights directement, continuez à le faire. La version 2.0 du [métapackage](xref:fundamentals/metapackage) inclut la dernière version d’Application Insights. Par conséquent, une erreur de passage à une version antérieure de package s’affiche si vous référencez une version antérieure.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationno-locidentity-improvements"></a>Adopter l’authentification/les Identity améliorations

ASP.NET Core 2,0 dispose d’un nouveau modèle d’authentification et d’un certain nombre de modifications significatives apportées à ASP.NET Core Identity . Si vous avez créé votre projet avec des comptes d’utilisateur individuels activés, ou si vous avez ajouté manuellement l’authentification ou Identity , consultez [migrer l’authentification et Identity vers ASP.net Core 2,0](xref:migration/1x-to-2x/identity-2x).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Changements importants dans ASP.NET Core 2.0](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
