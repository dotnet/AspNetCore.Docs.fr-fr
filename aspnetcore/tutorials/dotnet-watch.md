---
title: Développer des applications ASP.NET Core à l’aide d’un observateur de fichiers
author: rick-anderson
description: Ce tutoriel montre comment installer et utiliser l’outil Observateur de fichiers (dotnet watch) de l’interface de ligne de commande .NET Core dans une application ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 27420fe00ba6375e15b67fb359be06df055eff1f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060038"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Développer des applications ASP.NET Core à l’aide d’un observateur de fichiers

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` est un outil qui exécute une commande [CLI .net Core](/dotnet/core/tools) lorsque les fichiers sources changent. Par exemple, un changement de fichier peut déclencher la compilation, l’exécution de tests ou le déploiement.

Ce tutoriel utilise une API web existante avec deux points de terminaison : un qui retourne une somme et un qui retourne un produit. La méthode du produit comporte un bogue, qui est résolu dans ce tutoriel.

Téléchargez l' [exemple d’application](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Elle se compose de deux projets : *WebApp* (API web ASP.NET Core) et *WebAppTests* (API de tests unitaires pour le web).

Dans une interface de commande, accédez au dossier *WebApp* . Exécutez la commande suivante :

```dotnetcli
dotnet run
```

> [!NOTE]
> Vous pouvez utiliser `dotnet run --project <PROJECT>` pour spécifier un projet à exécuter. Par exemple, `dotnet run --project WebApp` à la racine de l’exemple d’application aura également pour effet d’exécuter le projet *WebApp* .

La sortie de la console affiche des messages semblables à ce qui suit (indiquant que l’application est en cours d’exécution et en attente de demandes) :

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Dans un navigateur web, accédez à `http://localhost:<port number>/api/math/sum?a=4&b=5`. Vous devez voir le résultat `9`.

Accédez à l’API du produit (`http://localhost:<port number>/api/math/product?a=4&b=5`). Elle retourne `9`, et non pas `20` comme prévu. Ce problème est résolu plus tard dans le tutoriel.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Ajouter `dotnet watch` à un projet

L’outil Observateur de fichiers `dotnet watch` est inclus dans la version 2.1.300 du kit SDK .NET Core. Les étapes suivantes sont requises quand vous utilisez une version antérieure du kit SDK .NET Core.

1. Ajoutez une référence de package `Microsoft.DotNet.Watcher.Tools` dans le fichier *.csproj*  :

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. Installez le package `Microsoft.DotNet.Watcher.Tools` en exécutant la commande suivante :

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Exécuter les commandes de l’interface CLI de .NET Core avec `dotnet watch`

Toutes les [commandes de l’interface CLI de .NET Core](/dotnet/core/tools#cli-commands) peuvent être exécutées avec `dotnet watch`. Exemple :

| Commande | Commande avec watch |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet Run-f netcoreapp 3.1 | exécution de dotnet Watch-f netcoreapp 3.1 |
| dotnet Run-f netcoreapp 3.1----Arg1 | dotnet Watch Run-f netcoreapp 3.1----Arg1 |
| dotnet test | dotnet watch test |

Exécutez `dotnet watch run` dans le dossier *WebApp* . La sortie de la console indique que `watch` a démarré.

::: moniker range=">= aspnetcore-5.0"
`dotnet watch run`L’exécution sur une application Web lance un navigateur qui navigue jusqu’à l’URL de l’application une fois que vous êtes prêt. `dotnet watch` pour cela, il lit la sortie de la console de l’application et attend le message Ready affiché par <xref:Microsoft.AspNetCore.WebHost> .

`dotnet watch` actualise le navigateur lorsqu’il détecte des modifications apportées aux fichiers surveillés. Pour ce faire, la commande Watch injecte un intergiciel (middleware) à l’application qui modifie les réponses HTML créées par l’application. L’intergiciel ajoute un bloc de script JavaScript à la page qui permet `dotnet watch` d’indiquer au navigateur d’actualiser. Actuellement, les modifications apportées à tous les fichiers surveillés, y compris le contenu statique tel que les fichiers. *HTML* et *. CSS* , provoquent la reconstruction de l’application.

`dotnet watch`:

* Surveille uniquement les fichiers qui ont un impact sur les builds par défaut.
* Tous les fichiers surveillés en plus (via la configuration) entraînent toujours une génération.

Pour plus d’informations sur la configuration, consultez la section relative à la [configuration de dotnet-Watch](#dotnet-watch-configuration) dans ce document.

::: moniker-end

> [!NOTE]
> Vous pouvez utiliser `dotnet watch --project <PROJECT>` pour spécifier un projet à surveiller. Par exemple, `dotnet watch --project WebApp run` à la racine de l’exemple d’application aura également pour effet d’exécuter le projet *WebApp* et d’en effectuer le suivi.

## <a name="make-changes-with-dotnet-watch"></a>Apporter des modifications avec `dotnet watch`

Vérifiez que `dotnet watch` est en cours d’exécution.

Corrigez le bogue présent dans la méthode `Product` de *MathController.cs* afin qu’elle retourne le produit et non la somme :

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Enregistrez le fichier. La sortie de la console indique que `dotnet watch` a détecté un changement de fichier et a redémarré l’application.

Vérifiez que `http://localhost:<port number>/api/math/product?a=4&b=5` retourne le résultat correct.

## <a name="run-tests-using-dotnet-watch"></a>Exécuter les tests à l’aide de `dotnet watch`

1. Changez la méthode `Product` de *MathController.cs* pour qu’elle retourne à nouveau la somme. Enregistrez le fichier.
1. Dans une interface de commande, accédez au dossier *WebAppTests* .
1. Exécutez [dotnet restore](/dotnet/core/tools/dotnet-restore).
1. Exécutez `dotnet watch test`. Sa sortie indique qu’un test a échoué et que l’observateur est en attente de changement de fichier :

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Corrigez le code de la méthode `Product` afin qu’elle retourne le produit. Enregistrez le fichier.

`dotnet watch` détecte le changement de fichier et réexécute les tests. La sortie de la console indique que les tests ont réussi.

## <a name="customize-files-list-to-watch"></a>Personnaliser la liste de fichiers à observer

Par défaut, `dotnet-watch` effectue le suivi de tous les fichiers qui correspondent aux modèles Glob suivants :

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Vous pouvez ajouter plusieurs éléments à la liste de suivi en modifiant le fichier *.csproj* . Vous pouvez spécifier des éléments individuellement ou en utilisant des modèles Glob.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Exclusion de fichiers à observer

`dotnet-watch` peut être configuré pour ignorer ses paramètres par défaut. Pour ignorer des fichiers spécifiques, ajoutez l’attribut `Watch="false"` à la définition d’un élément dans le fichier *.csproj* :

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a>Personnaliser les projets de suivi

`dotnet-watch` n’est pas limité aux projets C#. Vous pouvez créer des projets de suivi personnalisés pour gérer différents scénarios. Considérez l’organisation de projets suivante :

* **test**
  * *UnitTests/UnitTests.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

Si l’objectif est d’observer les deux projets, créez un fichier projet personnalisé configuré à cette fin :

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

Pour démarrer l’observation de fichiers sur les deux projets, passez au dossier *test* . Exécutez la commande suivante :

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest s’exécute quand n’importe quel fichier change dans un des projets de test.

## <a name="dotnet-watch-configuration"></a>configuration de dotnet-Watch

Certaines options de configuration peuvent être passées à `dotnet watch` via des variables d’environnement. Les variables disponibles sont les suivantes :

| Paramètre  | Description |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | Si la valeur est « 1 » ou « true », `dotnet watch` utilise un observateur de fichiers d’interrogation au lieu de CoreFx `FileSystemWatcher` . Utilisé lors de l’observation de fichiers sur des partages réseau ou des volumes montés par l’amarrage.                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | Par défaut, `dotnet watch` optimise la génération en évitant certaines opérations, telles que l’exécution de la restauration ou la réévaluation de l’ensemble des fichiers surveillés à chaque modification de fichier. Si la valeur est « 1 » ou « true », ces optimisations sont désactivées. |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | `dotnet watch run` tente de lancer des navigateurs pour les applications Web avec `launchBrowser` configuré dans *launchSettings.jssur* . Si la valeur est « 1 » ou « true », ce comportement est supprimé. |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | `dotnet watch run` tente d’actualiser les navigateurs lorsqu’il détecte des modifications de fichier. Si la valeur est « 1 » ou « true », ce comportement est supprimé. Ce comportement est également supprimé si `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` est défini. |

## <a name="dotnet-watch-in-github"></a>`dotnet-watch` dans GitHub

`dotnet-watch` fait partie du référentiel GitHub [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).
