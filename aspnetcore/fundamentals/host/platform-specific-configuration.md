---
title: Utiliser des assemblys de démarrage d’hébergement dans ASP.NET Core
author: rick-anderson
description: Découvrez comment améliorer une application ASP.NET Core à partir d’un assembly externe à l’aide d’une implémentation IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: c12487875db69472ee328dfc7a611ee99974c770
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061052"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Utiliser des assemblys de démarrage d’hébergement dans ASP.NET Core

Par [Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

Une <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implémentation (hébergement de démarrage) ajoute des améliorations à une application au démarrage à partir d’un assembly externe. Par exemple, une bibliothèque externe peut utiliser une implémentation d’hébergement au démarrage pour fournir des fournisseurs ou services de configuration supplémentaires à une application.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Attribut HostingStartup

Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indique la présence d’un assembly d’hébergement au démarrage à activer au moment de l’exécution.

L’assembly d’entrée ou l’assembly contenant la classe `Startup` est automatiquement analysé pour détecter l’attribut `HostingStartup`. La liste des assemblys où les attributs `HostingStartup` doivent être recherchés est chargée au moment de l’exécution à partir de la configuration spécifiée dans [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). La liste des assemblys à exclure de cette détection est chargée de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).

Dans l’exemple suivant, l’espace de noms de l’assembly d’hébergement au démarrage est `StartupEnhancement`. La classe contenant le code d’hébergement au démarrage est `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

L’attribut `HostingStartup` se trouve généralement dans le fichier de classe d’implémentation `IHostingStartup` de l’assembly d’hébergement au démarrage.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Découvrir les assemblys d’hébergement au démarrage chargés

Pour détecter les assemblys d’hébergement au démarrage chargés, activez la journalisation et analysez les journaux de l’application. Les erreurs qui se produisent durant le chargement des assemblys sont journalisées. Les assemblys d’hébergement au démarrage chargés sont journalisés au niveau Débogage, et toutes les erreurs sont journalisées.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Désactiver le chargement automatique des assemblys d’hébergement au démarrage

Pour désactiver le chargement automatique des assemblys d’hébergement au démarrage, choisissez l’une des approches suivantes :

* Pour bloquer le chargement de tous les assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sur `true` ou `1` :

  * Empêcher l’hébergement des paramètres de configuration de l’hôte de démarrage :

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * La variable d’environnement `ASPNETCORE_PREVENTHOSTINGSTARTUP`.

* Pour bloquer le chargement de certains assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sous la forme d’une liste délimitée par des points-virgules contenant les assemblys d’hébergement au démarrage à exclure au moment du démarrage :

  * Paramètre de configuration d’hôte d’exclusion des assemblys de démarrage d’hébergement :

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * La variable d’environnement `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Si le paramètre de configuration d’hôte et la variable d’environnement sont définis tous les deux, c’est le paramètre d’hôte qui détermine le comportement.

La désactivation des assemblys d’hébergement au démarrage à l’aide du paramètre d’hôte ou de la variable d’environnement désactive l’assembly globalement et peut donc désactiver plusieurs caractéristiques d’une application.

## <a name="project"></a>Project

Créez un hébergement au démarrage avec un des types de projet suivants :

* [Bibliothèque de classes](#class-library)
* [Application console sans point d’entrée](#console-app-without-an-entry-point)

### <a name="class-library"></a>Bibliothèque de classes

Une amélioration de l’hébergement au démarrage peut être fournie dans une bibliothèque de classes. La bibliothèque contient un attribut `HostingStartup`.

L' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) comprend une Razor application pages, *HostingStartupApp* et une bibliothèque de classes, *HostingStartupLibrary* . La bibliothèque de classes :

* Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`. `ServiceKeyInjection` Ajoute une paire de chaînes de service à la configuration de l’application à l’aide du fournisseur de configuration en mémoire ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Inclut un attribut `HostingStartup` qui identifie l’espace de noms et la classe d’hébergement au démarrage.

La `ServiceKeyInjection` méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application.

*HostingStartupLibrary/ServiceKeyInjection.cs* :

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage de la bibliothèque de classes :

*HostingStartupApp/Pages/Index.cshtml.cs* :

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclut également un projet de package NuGet qui fournit un hébergement au démarrage distinct, *HostingStartupPackage* . Le package a les mêmes caractéristiques que la bibliothèque de classes décrite précédemment. Le package :

* Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`. `ServiceKeyInjection` ajoute une paire de chaînes de service à la configuration de l’application.
* Inclut un attribut `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection.cs* :

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage du package :

*HostingStartupApp/Pages/Index.cshtml.cs* :

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Application console sans point d’entrée

*Cette approche s’applique aux applications .NET Core, et non à .NET Framework.*

Une amélioration d’hébergement au démarrage dynamique qui ne nécessite pas de référence au moment de la compilation pour l’activation peut être fournie dans une application console sans point d’entrée qui contient un attribut `HostingStartup`. La publication de l’application console génère un assembly d’hébergement au démarrage qui peut être utilisé à partir du magasin de runtime.

Une application console sans point d’entrée est utilisée dans ce processus, car :

* Un fichier de dépendances est nécessaire pour utiliser l’hébergement au démarrage dans l’assembly d’hébergement au démarrage. Un fichier de dépendances est une ressource d’application exécutable qui est générée par la publication d’une application, et non d’une bibliothèque.
* Une bibliothèque ne peut pas être ajoutée directement au [magasin de packages de runtime](/dotnet/core/deploying/runtime-store), qui nécessite un projet exécutable ciblant le runtime partagé.

Lors de la création d’un hébergement au démarrage dynamique :

* Un assembly d’hébergement au démarrage est créé à partir de l’application console sans point d’entrée qui :
  * Inclut une classe qui contient l’implémentation `IHostingStartup`.
  * Inclut un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pour identifier la classe d’implémentation `IHostingStartup`.
* L’application console est publiée pour obtenir les dépendances de l’hébergement au démarrage. La publication de l’application console entraîne la suppression des dépendances inutilisées dans le fichier de dépendances.
* Le fichier de dépendances est modifié pour définir l’emplacement d’exécution de l’assembly d’hébergement au démarrage.
* L’assembly d’hébergement au démarrage et le fichier de dépendances associé sont placés dans le magasin de packages de runtime. Pour permettre leur détection, l’assembly d’hébergement au démarrage et le fichier de dépendances associé sont référencés dans une paire de variables d’environnement.

L’application console référence le package [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifie une classe en tant qu’implémentation de `IHostingStartup` pour le chargement et l’exécution lors de la génération du <xref:Microsoft.AspNetCore.Hosting.IWebHost> . Dans l’exemple suivant, l’espace de noms est `StartupEnhancement`, et la classe est `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Une classe implémente `IHostingStartup`. La méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application. `IHostingStartup.Configure` dans l’assembly d’hébergement au démarrage est appelé par le runtime avant `Startup.Configure` dans le code utilisateur, ce qui permet au code utilisateur de remplacer la configuration fournie par l’assembly d’hébergement au démarrage.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Durant la génération d’un projet `IHostingStartup`, le fichier de dépendances ( *. deps.json* ) définit le dossier *bin* comme emplacement du `runtime` :

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Seule une partie du fichier est affichée. Le nom de l’assembly dans l’exemple est `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Configuration fournie par le démarrage d’hébergement

Il existe deux approches de gestion de la configuration selon si vous souhaitez que la configuration du démarrage d’hébergement ait la priorité ou que la configuration d’application ait la priorité :

1. Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pour charger la configuration après l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application. Avec cette approche, la configuration de démarrage d’hébergement est prioritaire sur la configuration d’application.
1. Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pour charger la configuration avant l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application. Avec cette approche, les valeurs de la configuration d’application sont prioritaires sur celles fournies par le démarrage d’hébergement.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Spécifier l’assembly d’hébergement au démarrage

Pour l’hébergement au démarrage fourni par une bibliothèque de classes ou une application console, spécifiez le nom de l’assembly d’hébergement au démarrage dans la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. Cette variable est spécifiée sous la forme d’une liste d’assemblys délimitée par des points-virgules.

L’analyse de détection de l’attribut `HostingStartup` porte uniquement sur les assemblys d’hébergement au démarrage. Dans l’exemple d’application ( *HostingStartupApp* ), pour permettre la détection des hébergements au démarrage décrits précédemment, la variable d’environnement est définie à la valeur suivante :

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Un assembly de démarrage d’hébergement peut également être défini à l’aide du paramètre de configuration d’hôte assemblys de démarrage d’hébergement :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

Lorsque plusieurs Assemblies de démarrage d’hébergement sont présents, leurs <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> méthodes sont exécutées dans l’ordre dans lequel les assemblys sont répertoriés.

## <a name="activation"></a>Activation

Les options d’activation de l’hébergement au démarrage sont les suivantes :

* [Magasin du runtime](#runtime-store): l’activation ne nécessite pas de référence au moment de la compilation pour l’activation. L’exemple d’application place les fichiers de l’assembly d’hébergement au démarrage et de ses dépendances dans le dossier *deployment* pour faciliter le déploiement de l’hébergement au démarrage dans un environnement multimachine. Le dossier *deployment* inclut également un script PowerShell qui crée ou modifie des variables d’environnement sur le système de déploiement pour activer l’hébergement au démarrage.
* Référence au moment de la compilation requise pour l’activation
  * [Package NuGet](#nuget-package)
  * [Dossier bin du projet](#project-bin-folder)

### <a name="runtime-store"></a>Magasin de runtime

L’implémentation d’hébergement au démarrage est placée dans le [magasin de runtime](/dotnet/core/deploying/runtime-store). L’application améliorée ne nécessite pas de référence à l’assembly au moment de la compilation.

Une fois l’hébergement au démarrage créé, un magasin de runtime est généré à l’aide du fichier manifeste de projet et de la commande [dotnet store](/dotnet/core/tools/dotnet-store).

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

Dans l’exemple d’application (projet *RuntimeStore* ), la commande suivante est utilisée :

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Pour que le runtime découvre le magasin de runtime, l’emplacement du magasin de runtime est ajouté à la variable d’environnement `DOTNET_SHARED_STORE`.

**Modifier et placer le fichier de dépendances de l’hébergement au démarrage**

Pour activer l’amélioration sans référence de package à l’amélioration, spécifiez les dépendances supplémentaires pour le runtime avec `additionalDeps`. `additionalDeps` vous permet de :

* Étendre le graphe de la bibliothèque de l’application en fournissant un ensemble de fichiers *.deps.json* supplémentaires à fusionner avec le fichier *.deps.json* de l’application au démarrage.
* Rendre l’assembly d’hébergement au démarrage détectable et chargeable.

L’approche recommandée pour la génération du fichier de dépendances supplémentaire est la suivante :

 1. Exécutez `dotnet publish` sur le fichier manifeste du magasin de runtime référencé dans la section précédente.
 1. Supprimez la référence de manifeste des bibliothèques et la `runtime` section du *.deps.jsrésultant sur* le fichier.

Dans l’exemple de projet, la propriété `store.manifest/1.0.0` est supprimée de la section `targets` et de la section `libraries` :

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Placez le fichier *.deps.json* à l’emplacement suivant :

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: Emplacement ajouté à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.
* `{SHARED FRAMEWORK NAME}`: Framework partagé requis pour ce fichier de dépendances supplémentaire.
* `{SHARED FRAMEWORK VERSION}`: Version minimale du Framework partagé.
* `{ENHANCEMENT ASSEMBLY NAME}`: Le nom de l’assembly de l’amélioration.

Dans l’exemple d’application (projet *RuntimeStore* ), le fichier de dépendances supplémentaire est placé à l’emplacement suivant :

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Pour que le runtime découvre l’emplacement du magasin de runtime, l’emplacement du fichier de dépendances supplémentaire est ajouté à la variable d’environnement `DOTNET_ADDITIONAL_DEPS`.

Dans l’exemple d’application (projet *RuntimeStore* ), la génération du magasin de runtime et du fichier de dépendances supplémentaire s’effectue à l’aide d’un script [PowerShell](/powershell/scripting/powershell-scripting).

Pour obtenir des exemples montrant comment définir des variables d’environnement pour différents systèmes d’exploitation, consultez [Utiliser plusieurs environnements](xref:fundamentals/environments).

**Déploiement**

Pour faciliter le déploiement d’un hébergement au démarrage dans un environnement multimachine, l’exemple d’application crée un dossier *deployment* dans la sortie publiée qui contient les éléments suivants :

* Le magasin de runtime d’hébergement au démarrage.
* Le fichier des dépendances de l’hébergement au démarrage.
* Un script PowerShell qui crée ou modifie les variables `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` et `DOTNET_ADDITIONAL_DEPS` pour prendre en charge l’activation de l’hébergement au démarrage. Exécutez ce script à partir d’une invite de commandes PowerShell d’administration sur le système de déploiement.

### <a name="nuget-package"></a>Package NuGet

Une amélioration de l’hébergement au démarrage peut être fournie dans un package NuGet. Le package a un attribut `HostingStartup`. Les types d’hébergement au démarrage fournis par le package sont mis à la disposition de l’application au moyen de l’une des approches suivantes :

* Le fichier projet de l’application améliorée crée une référence de package à l’hébergement au démarrage dans le fichier projet de l’application (référence au moment de la compilation). Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application ( *.deps.json* ). Cette approche s’applique à un package d’assembly d’hébergement au démarrage qui a été publié sur [nuget.org](https://www.nuget.org/).
* Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).

Pour plus d’informations sur les packages NuGet et le magasin de runtime, consultez les rubriques suivantes :

* [Création d’un Package NuGet avec les outils multiplateformes](/dotnet/core/deploying/creating-nuget-packages)
* [Publication de packages](/nuget/create-packages/publish-a-package)
* [Magasin de packages de runtime](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Dossier bin du projet

Une amélioration de l’hébergement au démarrage peut être fournie par un assembly déployé à partir du dossier *bin* dans l’application améliorée. Les types d’hébergement au démarrage fournis par l’assembly sont mis à la disposition de l’application au moyen de l’une des approches suivantes :

* Le fichier projet de l’application améliorée crée une référence d’assembly à l’hébergement au démarrage (référence au moment de la compilation). Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application ( *.deps.json* ). Cette approche s’applique lorsque le scénario de déploiement appelle dans le but d’effectuer une référence au moment de la compilation à l’assembly d’hébergement au démarrage (fichier *.dll* ) et en déplaçant l’assembly vers :
  * Le projet de consommation.
  * Un emplacement accessible par le projet de consommation.
* Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).
* Lors du ciblage de .NET Framework, l’assembly peut être chargé dans le contexte de charge par défaut, qui, sur .NET Framework, signifie que l’assembly se trouve à l’un des emplacements suivants :
  * Chemin de la base de l’application : dossier *bin* où se trouve l’exécutable de l’application ( *. exe* ).
  * Global assembly cache (GAC) : le GAC stocke les assemblys partagés par plusieurs applications .NET Framework. Pour plus d’informations, consultez [Comment : installer un assembly dans le global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) dans la documentation de .NET Framework.

## <a name="sample-code"></a>Exemple de code

[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Comment télécharger un exemple](xref:index#how-to-download-a-sample)) montre des scénarios d’implémentation de l’hébergement au démarrage :

* Deux assemblys d’hébergement au démarrage (bibliothèques de classes) définissent chacun une paire clé-valeur de configuration en mémoire :
  * Package NuGet ( *HostingStartupPackage* )
  * Bibliothèque de classes ( *HostingStartupLibrary* )
* Un hébergement au démarrage est activé à partir d’un assembly déployé depuis le magasin de runtime ( *StartupDiagnostics* ). L’assembly ajoute deux middleware (intergiciels) à l’application au démarrage, qui fournissent des informations de diagnostic :
  * Services inscrits
  * Adresse (schéma, hôte, chemin de base, chemin, chaîne de requête)
  * Connexion (adresse IP distante, port distant, adresse IP locale, port local, certificat client)
  * En-têtes de requête
  * Variables d'environnement

Pour exécuter l’exemple :

**Activation à partir d’un package NuGet**

1. Compilez le package *HostingStartupPackage* à l’aide de la commande [dotnet pack](/dotnet/core/tools/dotnet-pack).
1. Ajoutez le nom de l’assembly du package *HostingStartupPackage* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Compilez et exécutez l’application. Une référence de package est présente dans l’application améliorée (référence au moment de la compilation). Un `<PropertyGroup>` dans le fichier projet de l’application spécifie la sortie du projet de package ( *../HostingStartupPackage/bin/Debug* ) comme source de package. Cela permet à l’application d’utiliser le package sans télécharger le package sur [NuGet.org](https://www.nuget.org/). Pour plus d’informations, consultez les notes dans le fichier projet de HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` du package.

Si vous modifiez le projet *HostingStartupPackage* et le recompilez, effacez les caches locaux du package NuGet pour vous assurer que *HostingStartupApp* reçoit le package mis à jour et pas un package périmé du cache local. Pour effacer les caches NuGet locaux, exécutez la commande [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) suivante :

```dotnetcli
dotnet nuget locals all --clear
```

**Activation à partir d’une bibliothèque de classes**

1. Compilez la bibliothèque de classes *HostingStartupLibrary* à l’aide de la commande [dotnet build](/dotnet/core/tools/dotnet-build).
1. Ajoutez le nom de l’assembly de la bibliothèque de classes *HostingStartupLibrary* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. À partir du dossier *bin* , déployez l’assembly de la bibliothèque de classes dans l’application en copiant le fichier *HostingStartupLibrary.dll* du résultat de la compilation de la bibliothèque de classes dans le dossier *bin/Debug* de l’application.
1. Compilez et exécutez l’application. Un `<ItemGroup>` dans le fichier projet de l’application fait référence à l’assembly de la bibliothèque de classes ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll* ) (une référence au moment de la compilation). Pour plus d’informations, consultez les remarques dans le fichier projet de l’application HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` de la bibliothèque de classes.

**Activation à partir d’un assembly déployé à partir du magasin de runtime**

1. Le projet *StartupDiagnostics* utilise [PowerShell](/powershell/scripting/powershell-scripting) pour modifier le fichier *StartupDiagnostics.deps.json* associé. PowerShell est installé par défaut sur Windows à compter de Windows 7 SP1 et de Windows Server 2008 R2 SP1. Pour obtenir PowerShell sur d’autres plateformes, consultez [installation de différentes versions de PowerShell](/powershell/scripting/install/installing-powershell).
1. Exécutez le script *build.ps1* du dossier *RuntimeStore* . Le script :
   * Génère le `StartupDiagnostics` package dans le dossier *obj\packages* .
   * Génère le magasin de runtime de `StartupDiagnostics` dans le dossier *store* . La commande `dotnet store` du script utilise l’ [identificateur de runtime (RID)](/dotnet/core/rid-catalog)`win7-x64` pour un hébergement au démarrage déployé sur Windows. Si vous fournissez l’hébergement au démarrage pour un autre runtime, spécifiez l’identificateur du runtime à la ligne 37 du script. Le magasin du runtime pour `StartupDiagnostics` serait déplacé ultérieurement vers la Banque d’exécution du système ou de l’utilisateur sur l’ordinateur sur lequel l’assembly sera consommé. L’emplacement d’installation du magasin d’exécution de l’utilisateur pour l' `StartupDiagnostics` assembly est *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/StartupDiagnostics.dll* .
   * Génère le `additionalDeps` pour `StartupDiagnostics` dans le dossier *additionalDeps* . Les dépendances supplémentaires seraient ensuite déplacées vers les dépendances supplémentaires du système ou de l’utilisateur. L' `StartupDiagnostics` emplacement d’installation des dépendances supplémentaires de l’utilisateur est *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. Netcore. app/3.0.0/StartupDiagnostics.deps.jssur* .
   * Place le fichier *deploy.ps1* dans le dossier *deployment* .
1. Exécutez le script *deploy.ps1* du dossier *Deployment* . Le script ajoute :
   * `StartupDiagnostics` à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * Chemin d’accès des dépendances de démarrage d’hébergement (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.
   * Chemin d’accès du magasin du Runtime (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_SHARED_STORE` variable d’environnement.
1. Exécutez l’exemple d’application.
1. Demandez le point de terminaison `/services` pour voir les services inscrits de l’application. Demandez le point de terminaison `/diag` pour voir les informations de diagnostic.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Une <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implémentation (hébergement de démarrage) ajoute des améliorations à une application au démarrage à partir d’un assembly externe. Par exemple, une bibliothèque externe peut utiliser une implémentation d’hébergement au démarrage pour fournir des fournisseurs ou services de configuration supplémentaires à une application.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Attribut HostingStartup

Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) indique la présence d’un assembly d’hébergement au démarrage à activer au moment de l’exécution.

L’assembly d’entrée ou l’assembly contenant la classe `Startup` est automatiquement analysé pour détecter l’attribut `HostingStartup`. La liste des assemblys où les attributs `HostingStartup` doivent être recherchés est chargée au moment de l’exécution à partir de la configuration spécifiée dans [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey). La liste des assemblys à exclure de cette détection est chargée de [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey). Pour plus d’informations, consultez [Hôte web : Assemblys d’hébergement au démarrage](xref:fundamentals/host/web-host#hosting-startup-assemblies) et [Hôte web : Assemblys exclus de l’hébergement au démarrage](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

Dans l’exemple suivant, l’espace de noms de l’assembly d’hébergement au démarrage est `StartupEnhancement`. La classe contenant le code d’hébergement au démarrage est `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

L’attribut `HostingStartup` se trouve généralement dans le fichier de classe d’implémentation `IHostingStartup` de l’assembly d’hébergement au démarrage.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Découvrir les assemblys d’hébergement au démarrage chargés

Pour détecter les assemblys d’hébergement au démarrage chargés, activez la journalisation et analysez les journaux de l’application. Les erreurs qui se produisent durant le chargement des assemblys sont journalisées. Les assemblys d’hébergement au démarrage chargés sont journalisés au niveau Débogage, et toutes les erreurs sont journalisées.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Désactiver le chargement automatique des assemblys d’hébergement au démarrage

Pour désactiver le chargement automatique des assemblys d’hébergement au démarrage, choisissez l’une des approches suivantes :

* Pour bloquer le chargement de tous les assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sur `true` ou `1` :
  * Le paramètre de configuration d’hôte [PreventHostingStartup](xref:fundamentals/host/web-host#prevent-hosting-startup).
  * La variable d’environnement `ASPNETCORE_PREVENTHOSTINGSTARTUP`.
* Pour bloquer le chargement de certains assemblys d’hébergement au démarrage, définissez l’une des valeurs suivantes sous la forme d’une liste délimitée par des points-virgules contenant les assemblys d’hébergement au démarrage à exclure au moment du démarrage :
  * Le paramètre de configuration d’hôte [HostingStartupExcludeAssemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).
  * La variable d’environnement `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Si le paramètre de configuration d’hôte et la variable d’environnement sont définis tous les deux, c’est le paramètre d’hôte qui détermine le comportement.

La désactivation des assemblys d’hébergement au démarrage à l’aide du paramètre d’hôte ou de la variable d’environnement désactive l’assembly globalement et peut donc désactiver plusieurs caractéristiques d’une application.

## <a name="project"></a>Project

Créez un hébergement au démarrage avec un des types de projet suivants :

* [Bibliothèque de classes](#class-library)
* [Application console sans point d’entrée](#console-app-without-an-entry-point)

### <a name="class-library"></a>Bibliothèque de classes

Une amélioration de l’hébergement au démarrage peut être fournie dans une bibliothèque de classes. La bibliothèque contient un attribut `HostingStartup`.

L' [exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) comprend une Razor application pages, *HostingStartupApp* et une bibliothèque de classes, *HostingStartupLibrary* . La bibliothèque de classes :

* Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`. `ServiceKeyInjection` Ajoute une paire de chaînes de service à la configuration de l’application à l’aide du fournisseur de configuration en mémoire ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).
* Inclut un attribut `HostingStartup` qui identifie l’espace de noms et la classe d’hébergement au démarrage.

La `ServiceKeyInjection` méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application.

*HostingStartupLibrary/ServiceKeyInjection.cs* :

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage de la bibliothèque de classes :

*HostingStartupApp/Pages/Index.cshtml.cs* :

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) inclut également un projet de package NuGet qui fournit un hébergement au démarrage distinct, *HostingStartupPackage* . Le package a les mêmes caractéristiques que la bibliothèque de classes décrite précédemment. Le package :

* Contient une classe d’hébergement au démarrage, `ServiceKeyInjection`, qui implémente `IHostingStartup`. `ServiceKeyInjection` ajoute une paire de chaînes de service à la configuration de l’application.
* Inclut un attribut `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection.cs* :

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

La page d’index de l’application lit et affiche les valeurs de configuration des deux clés définies par l’assembly d’hébergement au démarrage du package :

*HostingStartupApp/Pages/Index.cshtml.cs* :

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Application console sans point d’entrée

*Cette approche s’applique aux applications .NET Core, et non à .NET Framework.*

Une amélioration d’hébergement au démarrage dynamique qui ne nécessite pas de référence au moment de la compilation pour l’activation peut être fournie dans une application console sans point d’entrée qui contient un attribut `HostingStartup`. La publication de l’application console génère un assembly d’hébergement au démarrage qui peut être utilisé à partir du magasin de runtime.

Une application console sans point d’entrée est utilisée dans ce processus, car :

* Un fichier de dépendances est nécessaire pour utiliser l’hébergement au démarrage dans l’assembly d’hébergement au démarrage. Un fichier de dépendances est une ressource d’application exécutable qui est générée par la publication d’une application, et non d’une bibliothèque.
* Une bibliothèque ne peut pas être ajoutée directement au [magasin de packages de runtime](/dotnet/core/deploying/runtime-store), qui nécessite un projet exécutable ciblant le runtime partagé.

Lors de la création d’un hébergement au démarrage dynamique :

* Un assembly d’hébergement au démarrage est créé à partir de l’application console sans point d’entrée qui :
  * Inclut une classe qui contient l’implémentation `IHostingStartup`.
  * Inclut un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) pour identifier la classe d’implémentation `IHostingStartup`.
* L’application console est publiée pour obtenir les dépendances de l’hébergement au démarrage. La publication de l’application console entraîne la suppression des dépendances inutilisées dans le fichier de dépendances.
* Le fichier de dépendances est modifié pour définir l’emplacement d’exécution de l’assembly d’hébergement au démarrage.
* L’assembly d’hébergement au démarrage et le fichier de dépendances associé sont placés dans le magasin de packages de runtime. Pour permettre leur détection, l’assembly d’hébergement au démarrage et le fichier de dépendances associé sont référencés dans une paire de variables d’environnement.

L’application console référence le package [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

Un attribut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identifie une classe en tant qu’implémentation de `IHostingStartup` pour le chargement et l’exécution lors de la génération du <xref:Microsoft.AspNetCore.Hosting.IWebHost> . Dans l’exemple suivant, l’espace de noms est `StartupEnhancement`, et la classe est `StartupEnhancementHostingStartup` :

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Une classe implémente `IHostingStartup`. La méthode de la classe <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> utilise un <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> pour ajouter des améliorations à une application. `IHostingStartup.Configure` dans l’assembly d’hébergement au démarrage est appelé par le runtime avant `Startup.Configure` dans le code utilisateur, ce qui permet au code utilisateur de remplacer la configuration fournie par l’assembly d’hébergement au démarrage.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Durant la génération d’un projet `IHostingStartup`, le fichier de dépendances ( *. deps.json* ) définit le dossier *bin* comme emplacement du `runtime` :

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Seule une partie du fichier est affichée. Le nom de l’assembly dans l’exemple est `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Configuration fournie par le démarrage d’hébergement

Il existe deux approches de gestion de la configuration selon si vous souhaitez que la configuration du démarrage d’hébergement ait la priorité ou que la configuration d’application ait la priorité :

1. Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> pour charger la configuration après l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application. Avec cette approche, la configuration de démarrage d’hébergement est prioritaire sur la configuration d’application.
1. Fournissez la configuration à l’application à l’aide de <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> pour charger la configuration avant l’exécution des délégués <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de l’application. Avec cette approche, les valeurs de la configuration d’application sont prioritaires sur celles fournies par le démarrage d’hébergement.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Spécifier l’assembly d’hébergement au démarrage

Pour l’hébergement au démarrage fourni par une bibliothèque de classes ou une application console, spécifiez le nom de l’assembly d’hébergement au démarrage dans la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. Cette variable est spécifiée sous la forme d’une liste d’assemblys délimitée par des points-virgules.

L’analyse de détection de l’attribut `HostingStartup` porte uniquement sur les assemblys d’hébergement au démarrage. Dans l’exemple d’application ( *HostingStartupApp* ), pour permettre la détection des hébergements au démarrage décrits précédemment, la variable d’environnement est définie à la valeur suivante :

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Un assembly d’hébergement au démarrage peut également être défini à l’aide du paramètre de configuration d’hôte [HostingStartupAssemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies).

Lorsque plusieurs Assemblies de démarrage d’hébergement sont présents, leurs <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> méthodes sont exécutées dans l’ordre dans lequel les assemblys sont répertoriés.

## <a name="activation"></a>Activation

Les options d’activation de l’hébergement au démarrage sont les suivantes :

* [Magasin du runtime](#runtime-store): l’activation ne nécessite pas de référence au moment de la compilation pour l’activation. L’exemple d’application place les fichiers de l’assembly d’hébergement au démarrage et de ses dépendances dans le dossier *deployment* pour faciliter le déploiement de l’hébergement au démarrage dans un environnement multimachine. Le dossier *deployment* inclut également un script PowerShell qui crée ou modifie des variables d’environnement sur le système de déploiement pour activer l’hébergement au démarrage.
* Référence au moment de la compilation requise pour l’activation
  * [Package NuGet](#nuget-package)
  * [Dossier bin du projet](#project-bin-folder)

### <a name="runtime-store"></a>Magasin de runtime

L’implémentation d’hébergement au démarrage est placée dans le [magasin de runtime](/dotnet/core/deploying/runtime-store). L’application améliorée ne nécessite pas de référence à l’assembly au moment de la compilation.

Une fois l’hébergement au démarrage créé, un magasin de runtime est généré à l’aide du fichier manifeste de projet et de la commande [dotnet store](/dotnet/core/tools/dotnet-store).

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

Dans l’exemple d’application (projet *RuntimeStore* ), la commande suivante est utilisée :

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Pour que le runtime découvre le magasin de runtime, l’emplacement du magasin de runtime est ajouté à la variable d’environnement `DOTNET_SHARED_STORE`.

**Modifier et placer le fichier de dépendances de l’hébergement au démarrage**

Pour activer l’amélioration sans référence de package à l’amélioration, spécifiez les dépendances supplémentaires pour le runtime avec `additionalDeps`. `additionalDeps` vous permet de :

* Étendre le graphe de la bibliothèque de l’application en fournissant un ensemble de fichiers *.deps.json* supplémentaires à fusionner avec le fichier *.deps.json* de l’application au démarrage.
* Rendre l’assembly d’hébergement au démarrage détectable et chargeable.

L’approche recommandée pour la génération du fichier de dépendances supplémentaire est la suivante :

 1. Exécutez `dotnet publish` sur le fichier manifeste du magasin de runtime référencé dans la section précédente.
 1. Supprimez la référence de manifeste des bibliothèques et la `runtime` section du *.deps.jsrésultant sur* le fichier.

Dans l’exemple de projet, la propriété `store.manifest/1.0.0` est supprimée de la section `targets` et de la section `libraries` :

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Placez le fichier *.deps.json* à l’emplacement suivant :

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: Emplacement ajouté à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.
* `{SHARED FRAMEWORK NAME}`: Framework partagé requis pour ce fichier de dépendances supplémentaire.
* `{SHARED FRAMEWORK VERSION}`: Version minimale du Framework partagé.
* `{ENHANCEMENT ASSEMBLY NAME}`: Le nom de l’assembly de l’amélioration.

Dans l’exemple d’application (projet *RuntimeStore* ), le fichier de dépendances supplémentaire est placé à l’emplacement suivant :

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Pour que le runtime découvre l’emplacement du magasin de runtime, l’emplacement du fichier de dépendances supplémentaire est ajouté à la variable d’environnement `DOTNET_ADDITIONAL_DEPS`.

Dans l’exemple d’application (projet *RuntimeStore* ), la génération du magasin de runtime et du fichier de dépendances supplémentaire s’effectue à l’aide d’un script [PowerShell](/powershell/scripting/powershell-scripting).

Pour obtenir des exemples montrant comment définir des variables d’environnement pour différents systèmes d’exploitation, consultez [Utiliser plusieurs environnements](xref:fundamentals/environments).

**Déploiement**

Pour faciliter le déploiement d’un hébergement au démarrage dans un environnement multimachine, l’exemple d’application crée un dossier *deployment* dans la sortie publiée qui contient les éléments suivants :

* Le magasin de runtime d’hébergement au démarrage.
* Le fichier des dépendances de l’hébergement au démarrage.
* Un script PowerShell qui crée ou modifie les variables `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` et `DOTNET_ADDITIONAL_DEPS` pour prendre en charge l’activation de l’hébergement au démarrage. Exécutez ce script à partir d’une invite de commandes PowerShell d’administration sur le système de déploiement.

### <a name="nuget-package"></a>Package NuGet

Une amélioration de l’hébergement au démarrage peut être fournie dans un package NuGet. Le package a un attribut `HostingStartup`. Les types d’hébergement au démarrage fournis par le package sont mis à la disposition de l’application au moyen de l’une des approches suivantes :

* Le fichier projet de l’application améliorée crée une référence de package à l’hébergement au démarrage dans le fichier projet de l’application (référence au moment de la compilation). Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application ( *.deps.json* ). Cette approche s’applique à un package d’assembly d’hébergement au démarrage qui a été publié sur [nuget.org](https://www.nuget.org/).
* Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).

Pour plus d’informations sur les packages NuGet et le magasin de runtime, consultez les rubriques suivantes :

* [Création d’un Package NuGet avec les outils multiplateformes](/dotnet/core/deploying/creating-nuget-packages)
* [Publication de packages](/nuget/create-packages/publish-a-package)
* [Magasin de packages de runtime](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Dossier bin du projet

Une amélioration de l’hébergement au démarrage peut être fournie par un assembly déployé à partir du dossier *bin* dans l’application améliorée. Les types d’hébergement au démarrage fournis par l’assembly sont mis à la disposition de l’application au moyen de l’une des approches suivantes :

* Le fichier projet de l’application améliorée crée une référence d’assembly à l’hébergement au démarrage (référence au moment de la compilation). Une fois la référence au moment de la compilation créée, l’assembly d’hébergement au démarrage et toutes ses dépendances sont ajoutés au fichier de dépendances de l’application ( *.deps.json* ). Cette approche s’applique lorsque le scénario de déploiement appelle dans le but d’effectuer une référence au moment de la compilation à l’assembly d’hébergement au démarrage (fichier *.dll* ) et en déplaçant l’assembly vers :
  * Le projet de consommation.
  * Un emplacement accessible par le projet de consommation.
* Le fichier de dépendances de l’hébergement au démarrage est mis à la disposition de l’application améliorée de la façon décrite dans la section [Magasin de runtime](#runtime-store) (sans référence au moment de la compilation).
* Lors du ciblage de .NET Framework, l’assembly peut être chargé dans le contexte de charge par défaut, qui, sur .NET Framework, signifie que l’assembly se trouve à l’un des emplacements suivants :
  * Chemin de la base de l’application : dossier *bin* où se trouve l’exécutable de l’application ( *. exe* ).
  * Global assembly cache (GAC) : le GAC stocke les assemblys partagés par plusieurs applications .NET Framework. Pour plus d’informations, consultez [Comment : installer un assembly dans le global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) dans la documentation de .NET Framework.

## <a name="sample-code"></a>Exemple de code

[L’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Comment télécharger un exemple](xref:index#how-to-download-a-sample)) montre des scénarios d’implémentation de l’hébergement au démarrage :

* Deux assemblys d’hébergement au démarrage (bibliothèques de classes) définissent chacun une paire clé-valeur de configuration en mémoire :
  * Package NuGet ( *HostingStartupPackage* )
  * Bibliothèque de classes ( *HostingStartupLibrary* )
* Un hébergement au démarrage est activé à partir d’un assembly déployé depuis le magasin de runtime ( *StartupDiagnostics* ). L’assembly ajoute deux middleware (intergiciels) à l’application au démarrage, qui fournissent des informations de diagnostic :
  * Services inscrits
  * Adresse (schéma, hôte, chemin de base, chemin, chaîne de requête)
  * Connexion (adresse IP distante, port distant, adresse IP locale, port local, certificat client)
  * En-têtes de requête
  * Variables d'environnement

Pour exécuter l’exemple :

**Activation à partir d’un package NuGet**

1. Compilez le package *HostingStartupPackage* à l’aide de la commande [dotnet pack](/dotnet/core/tools/dotnet-pack).
1. Ajoutez le nom de l’assembly du package *HostingStartupPackage* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Compilez et exécutez l’application. Une référence de package est présente dans l’application améliorée (référence au moment de la compilation). Un `<PropertyGroup>` dans le fichier projet de l’application spécifie la sortie du projet de package ( *../HostingStartupPackage/bin/Debug* ) comme source de package. Cela permet à l’application d’utiliser le package sans télécharger le package sur [NuGet.org](https://www.nuget.org/). Pour plus d’informations, consultez les notes dans le fichier projet de HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` du package.

Si vous modifiez le projet *HostingStartupPackage* et le recompilez, effacez les caches locaux du package NuGet pour vous assurer que *HostingStartupApp* reçoit le package mis à jour et pas un package périmé du cache local. Pour effacer les caches NuGet locaux, exécutez la commande [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) suivante :

```dotnetcli
dotnet nuget locals all --clear
```

**Activation à partir d’une bibliothèque de classes**

1. Compilez la bibliothèque de classes *HostingStartupLibrary* à l’aide de la commande [dotnet build](/dotnet/core/tools/dotnet-build).
1. Ajoutez le nom de l’assembly de la bibliothèque de classes *HostingStartupLibrary* à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. À partir du dossier *bin* , déployez l’assembly de la bibliothèque de classes dans l’application en copiant le fichier *HostingStartupLibrary.dll* du résultat de la compilation de la bibliothèque de classes dans le dossier *bin/Debug* de l’application.
1. Compilez et exécutez l’application. Dans le fichier projet de l’application, un `<ItemGroup>` référence l’assembly de la bibliothèque de classes ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll* ) (référence au moment de la compilation). Pour plus d’informations, consultez les remarques dans le fichier projet de l’application HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Notez que les valeurs des clés de configuration de service affichées dans la page d’index correspondent aux valeurs définies par la méthode `ServiceKeyInjection.Configure` de la bibliothèque de classes.

**Activation à partir d’un assembly déployé à partir du magasin de runtime**

1. Le projet *StartupDiagnostics* utilise [PowerShell](/powershell/scripting/powershell-scripting) pour modifier le fichier *StartupDiagnostics.deps.json* associé. PowerShell est installé par défaut sur Windows à compter de Windows 7 SP1 et de Windows Server 2008 R2 SP1. Pour obtenir PowerShell sur d’autres plateformes, consultez [installation de différentes versions de PowerShell](/powershell/scripting/install/installing-powershell).
1. Exécutez le script *build.ps1* du dossier *RuntimeStore* . Le script :
   * Génère le `StartupDiagnostics` package dans le dossier *obj\packages* .
   * Génère le magasin de runtime de `StartupDiagnostics` dans le dossier *store* . La commande `dotnet store` du script utilise l’ [identificateur de runtime (RID)](/dotnet/core/rid-catalog)`win7-x64` pour un hébergement au démarrage déployé sur Windows. Si vous fournissez l’hébergement au démarrage pour un autre runtime, spécifiez l’identificateur du runtime à la ligne 37 du script. Le magasin du runtime pour `StartupDiagnostics` serait déplacé ultérieurement vers la Banque d’exécution du système ou de l’utilisateur sur l’ordinateur sur lequel l’assembly sera consommé. L’emplacement d’installation du magasin d’exécution de l’utilisateur pour l' `StartupDiagnostics` assembly est *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/StartupDiagnostics.dll* .
   * Génère le `additionalDeps` pour `StartupDiagnostics` dans le dossier *additionalDeps* . Les dépendances supplémentaires seraient ensuite déplacées vers les dépendances supplémentaires du système ou de l’utilisateur. L' `StartupDiagnostics` emplacement d’installation des dépendances supplémentaires de l’utilisateur est *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. Netcore. app/2.2.0/StartupDiagnostics.deps.jssur* .
   * Place le fichier *deploy.ps1* dans le dossier *deployment* .
1. Exécutez le script *deploy.ps1* du dossier *Deployment* . Le script ajoute :
   * `StartupDiagnostics` à la variable d’environnement `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * Chemin d’accès des dépendances de démarrage d’hébergement (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_ADDITIONAL_DEPS` variable d’environnement.
   * Chemin d’accès du magasin du Runtime (dans le dossier de *déploiement* du projet RuntimeStore) à la `DOTNET_SHARED_STORE` variable d’environnement.
1. Exécutez l’exemple d’application.
1. Demandez le point de terminaison `/services` pour voir les services inscrits de l’application. Demandez le point de terminaison `/diag` pour voir les informations de diagnostic.

::: moniker-end
