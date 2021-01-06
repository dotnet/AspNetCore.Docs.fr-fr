---
title: commande dotnet aspnet-codegenerator
author: rick-anderson
description: La commande dotnet aspnet-codegenerator structure des projets ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94920701"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator` - Exécute le moteur de génération de modèles automatique ASP.NET Core. `dotnet aspnet-codegenerator` étant uniquement requis pour générer automatiquement des modèles à partir de la ligne de commande, il n’est pas nécessaire d’utiliser la génération de modèles automatique avec Visual Studio.

## <a name="install-and-update-aspnet-codegenerator"></a>Installer et mettre à jour ASPNET-CodeGenerator

Installez le [Kit de développement logiciel (SDK) .net](https://dotnet.microsoft.com/download).

`dotnet-aspnet-codegenerator` est un [outil global](/dotnet/core/tools/global-tools) qui doit être installé. La commande suivante installe la dernière version stable de l’outil `dotnet-aspnet-codegenerator` :

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

La commande suivante met à jour `dotnet-aspnet-codegenerator` vers la dernière version stable disponible à partir du SDK .NET Core installé :

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a>Désinstaller ASPNET-CodeGenerator

Il peut être nécessaire de désinstaller `aspnet-codegenerator` pour résoudre les problèmes. Par exemple, si vous avez installé une version préliminaire de `aspnet-codegenerator` , désinstallez-la avant d’installer la version finale.

Les commandes suivantes `dotnet-aspnet-codegenerator` désinstallent l’outil et installent la dernière version stable :

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Synopsis

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Description

La commande globale `dotnet aspnet-codegenerator` exécute le générateur de code ASP.NET Core et le moteur de génération de modèles automatique.

## <a name="arguments"></a>Arguments

`generator`

Le générateur de code à effectuer. Les générateurs suivants sont disponibles :

| Générateur  | Opération                                                            |
| ---------- | -------------------------------------------------------------------- |
| superficie       | [Génération de modèles automatique pour une zone](xref:mvc/controllers/areas)                      |
| contrôleur | [Génération de modèles automatique pour un contrôleur](xref:tutorials/first-mvc-app/adding-model)  |
| identité   | [Structure Identity](xref:security/authentication/scaffold-identity) |
| razorpage  | [Pages de modèles Razor](xref:tutorials/razor-pages/model)            |
| vue       | [Génération de modèles automatique pour une vue](xref:mvc/views/overview)                          |

## <a name="options"></a>Options

`-n|--nuget-package-dir`

Spécifie le répertoire du package NuGet.

`-c|--configuration {Debug|Release}`

Définit la configuration de build. La valeur par défaut est `Debug`.

`-tfm|--target-framework`

[Framework](/dotnet/standard/frameworks) cible à utiliser. Par exemple : `net46`.

`-b|--build-base-path`

Le chemin de base de génération.

`-h|--help`

Affiche une aide brève pour la commande.

`--no-build`

Ne génère pas le projet avant l’exécution. L’indicateur `--no-restore` est également défini implicitement.

`-p|--project <PATH>`

Spécifie le chemin du fichier projet à exécuter (nom de dossier ou chemin complet). Si aucune valeur n’est spécifiée, le répertoire actif est utilisé par défaut.

## <a name="generator-options"></a>Options du générateur

Les sections suivantes décrivent en détail les options disponibles pour les générateurs pris en charge :

* Domaine
* Contrôleur
* Identity  
* Razorpage
* Affichage

<a name="area"></a>

### <a name="area-options"></a>Options de zone

Cet outil est conçu pour les projets web ASP.NET Core avec des contrôleurs et des vues. Elle n’est pas destinée aux Razor applications pages.

Utilisation : `dotnet aspnet-codegenerator area AreaNameToGenerate`

La commande précédente génère les dossiers suivants :

* *Régions*
  * *AreaNameToGenerate*
    * *Controllers*
    * *Données*
    * *Modèles*
    * *Views*

<a name="ctl"></a>

### <a name="controller-options"></a>Options de contrôleur

Le tableau suivant répertorie les options pour  `aspnet-codegenerator` `controller` et `razorpage` :

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Le tableau ci-dessous répertorie les options uniques à `aspnet-codegenerator controller` :

| Option                         | Description                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| --controllerName ou -name      | Nom du contrôleur.                                                                                   |
| --useAsyncActions ou -async    | Générer des actions asynchrones du contrôleur.                                                                        |
| --noViews ou -nv               | Ne générer **aucune** vue.                                                                                    |
| --restWithNoViews ou -api      | Générer un contrôleur avec l’API de style REST. `noViews` est supposé et les options associées à la vue sont ignorées. |
| --readWriteActions ou -actions | Générer un contrôleur avec actions en lecture/écriture sans modèle.                                              |

Utilisez le commutateur `-h` pour obtenir de l’aide sur la commande `aspnet-codegenerator controller` :

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Consultez [Générer automatiquement le modèle de film](xref:tutorials/first-mvc-app/adding-model) pour obtenir un exemple de `dotnet aspnet-codegenerator controller`.

### <a name="no-locrazorpage"></a>Razorpage

<a name="rp"></a>

Razor Les pages peuvent être structurées individuellement en spécifiant le nom de la nouvelle page et le modèle à utiliser. Les modèles pris en charge sont :

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Par exemple, la commande suivante utilise le modèle de modification pour générer *MyEdit.cshtml* et *MyEdit.cshtml.cs* :

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

En règle générale, le modèle et le nom de fichier générés ne sont pas spécifiés, et les modèles suivants sont créés :

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Le tableau suivant répertorie les options pour  `aspnet-codegenerator` `razorpage` et `controller` :

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Le tableau ci-dessous répertorie les options uniques à `aspnet-codegenerator razorpage` :

| Option                        | Description                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| --namespaceName ou -namespace | Nom de l’espace de noms à utiliser pour le PageModel généré                          |
| --partialView ou -partial     | Générer une vue partielle. Les options de mise en page -l et -udl sont ignorées si ceci est spécifié. |
| --noPageModel ou -npm         | Choisir de ne pas générer une classe PageModel pour le modèle vide                           |

Utilisez le commutateur `-h` pour obtenir de l’aide sur la commande `aspnet-codegenerator razorpage` :

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Consultez [Générer automatiquement le modèle de film](xref:tutorials/razor-pages/model) pour obtenir un exemple de `dotnet aspnet-codegenerator razorpage`.

### Identity

Voir [l' Identity échafaudage](xref:security/authentication/scaffold-identity)
