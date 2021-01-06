---
title: Utiliser l’interface CLI LibMan avec ASP.NET Core
author: scottaddie
description: Découvrez comment utiliser l’interface CLI LibMan dans un projet ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
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
uid: client-side/libman/libman-cli
ms.openlocfilehash: dad9136439b61ad98523061d181fe44d3bf1273d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054747"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Utiliser l’interface CLI LibMan avec ASP.NET Core

Par [Scott Addie](https://twitter.com/Scott_Addie)

L’interface CLI [LibMan](xref:client-side/libman/index) est un outil multiplateforme qui est pris en charge partout où .net Core est pris en charge.

## <a name="prerequisites"></a>Conditions préalables requises

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Installation

Pour installer l’interface CLI LibMan :

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

Un [outil Global .net Core](/dotnet/core/tools/global-tools#install-a-global-tool) est installé à partir du package NuGet [Microsoft. Web. librarymanager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .

Pour installer l’interface CLI LibMan à partir d’une source de package NuGet spécifique :

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

Dans l’exemple précédent, un outil Global .NET Core est installé à partir du fichier *C:\Temp\Microsoft.Web.librarymanager.cli.1.0.94-g606058a278.nupkg* de l’ordinateur Windows local.

## <a name="usage"></a>Usage

Une fois l’installation de l’interface CLI réussie, vous pouvez utiliser la commande suivante :

```console
libman
```

Pour afficher la version installée de l’interface de commande :

```console
libman --version
```

Pour afficher les commandes CLI disponibles :

```console
libman --help
```

La commande précédente affiche une sortie similaire à ce qui suit :

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

Les sections suivantes décrivent les commandes CLI disponibles.

## <a name="initialize-libman-in-the-project"></a>Initialiser LibMan dans le projet

La `libman init` commande crée un *libman.jssur* le fichier s’il n’en existe pas. Le fichier est créé avec le contenu du modèle d’élément par défaut.

### <a name="synopsis"></a>Synopsis

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman init` :

* `-d|--default-destination <PATH>`

  Chemin d’accès relatif au dossier actif. Les fichiers de bibliothèque sont installés à cet emplacement si aucune `destination` propriété n’est définie pour une bibliothèque dans *libman.jssur*. La `<PATH>` valeur est écrite dans la `defaultDestination` propriété de *libman.js*.

* `-p|--default-provider <PROVIDER>`

  Fournisseur à utiliser si aucun fournisseur n’est défini pour une bibliothèque donnée. La `<PROVIDER>` valeur est écrite dans la `defaultProvider` propriété de *libman.js*. Remplacez `<PROVIDER>` par l’une des valeurs suivantes :

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

Pour créer un *libman.jssur* un fichier dans un projet ASP.net Core :

* Accédez à la racine du projet.
* Exécutez la commande suivante :

  ```console
  libman init
  ```

* Tapez le nom du fournisseur par défaut ou appuyez sur `Enter` pour utiliser le fournisseur CDNJS par défaut. Les valeurs valides sont les suivantes :

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![commande Libman init-fournisseur par défaut](_static/libman-init-provider.png)

Un *libman.jssur* le fichier est ajouté à la racine du projet avec le contenu suivant :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Ajouter des fichiers de bibliothèque

La `libman install` commande télécharge et installe les fichiers de bibliothèque dans le projet. Un *libman.jssur le* fichier est ajouté s’il n’en existe pas. La *libman.jssur* le fichier est modifiée pour stocker les détails de configuration des fichiers de la bibliothèque.

### <a name="synopsis"></a>Synopsis

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Nom de la bibliothèque à installer. Ce nom peut inclure la notation du numéro de version (par exemple, `@1.2.0` ).

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman install` :

* `-d|--destination <PATH>`

  Emplacement d’installation de la bibliothèque. S’il n’est pas spécifié, l’emplacement par défaut est utilisé. Si aucune `defaultDestination` propriété n’est spécifiée dans *libman.jssur*, cette option est obligatoire.

* `--files <FILE>`

  Spécifiez le nom du fichier à installer à partir de la bibliothèque. S’il n’est pas spécifié, tous les fichiers de la bibliothèque sont installés. Fournissez une `--files` option par fichier à installer. Les chemins d’accès relatifs sont également pris en charge. Par exemple : `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Nom du fournisseur à utiliser pour l’acquisition de la bibliothèque. Remplacez `<PROVIDER>` par l’une des valeurs suivantes :
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  S’il n’est pas spécifié, la `defaultProvider` propriété de *libman.jssur* est utilisée. Si aucune `defaultProvider` propriété n’est spécifiée dans *libman.jssur*, cette option est obligatoire.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

Prenez en compte les *libman.jssuivantes sur* le fichier :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Pour installer le fichier de *jquery.min.js* jQuery version 3.2.1 dans le dossier *wwwroot/scripts/jQuery* à l’aide du fournisseur CDNJS :

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Le *libman.jssur* le fichier ressemble à ce qui suit :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

Pour installer les fichiers *calendar.js* et *Calendar. CSS* à partir de *C : \\ temp \\ contosoCalendar \\* à l’aide du fournisseur de système de fichiers :

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

L’invite suivante s’affiche pour deux raisons :

* Le *libman.jssur* le fichier ne contient pas de `defaultDestination` propriété.
* La `libman install` commande ne contient pas l' `-d|--destination` option.

![commande d’installation de Libman-destination](_static/libman-install-destination.png)

Après avoir accepté la destination par défaut, le *libman.jssur* le fichier ressemble à ce qui suit :

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a>Restaurer les fichiers de bibliothèque

La `libman restore` commande installe les fichiers de bibliothèque définis dans *libman.jssur*. Les règles suivantes s’appliquent :

* S’il n’existe aucun *libman.jssur* le fichier dans la racine du projet, une erreur est retournée.
* Si une bibliothèque spécifie un fournisseur, la `defaultProvider` propriété de *libman.jssur* est ignorée.
* Si une bibliothèque spécifie une destination, la `defaultDestination` propriété de *libman.jssur* est ignorée.

### <a name="synopsis"></a>Synopsis

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman restore` :

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

Pour restaurer les fichiers de bibliothèque définis dans *libman.jssur*:

```console
libman restore
```

## <a name="delete-library-files"></a>Supprimer les fichiers de bibliothèque

La `libman clean` commande supprime les fichiers de bibliothèque précédemment restaurés via LibMan. Dossiers qui deviennent vides après la suppression de cette opération. Les configurations associées aux fichiers de bibliothèque dans la `libraries` propriété de *libman.jssur* ne sont pas supprimées.

### <a name="synopsis"></a>Synopsis

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman clean` :

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

Pour supprimer les fichiers de bibliothèque installés via LibMan :

```console
libman clean
```

## <a name="uninstall-library-files"></a>Désinstaller les fichiers de bibliothèque

`libman uninstall`Commande :

* Supprime de la destination tous les fichiers associés à la bibliothèque spécifiée dans *libman.js*.
* Supprime la configuration de bibliothèque associée de *libman.js*.

Une erreur se produit dans les cas suivants :

* Il n’existe aucun *libman.jssur* le fichier dans la racine du projet.
* La bibliothèque spécifiée n’existe pas.

Si plusieurs bibliothèques portant le même nom sont installées, vous êtes invité à en choisir une.

### <a name="synopsis"></a>Synopsis

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Nom de la bibliothèque à désinstaller. Ce nom peut inclure la notation du numéro de version (par exemple, `@1.2.0` ).

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman uninstall` :

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

Prenez en compte les *libman.jssuivantes sur* le fichier :

[!code-json[](samples/LibManSample/libman.json)]

* Pour désinstaller jQuery, l’une des commandes suivantes est réussie :

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Pour désinstaller les fichiers Lodash installés via le `filesystem` fournisseur :

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Version de la bibliothèque de mises à jour

La `libman update` commande met à jour une bibliothèque installée via LibMan avec la version spécifiée.

Une erreur se produit dans les cas suivants :

* Il n’existe aucun *libman.jssur* le fichier dans la racine du projet.
* La bibliothèque spécifiée n’existe pas.

Si plusieurs bibliothèques portant le même nom sont installées, vous êtes invité à en choisir une.

### <a name="synopsis"></a>Synopsis

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Arguments

`LIBRARY`

Nom de la bibliothèque à mettre à jour.

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman update` :

* `-pre`

  Obtenez la dernière version préliminaire de la bibliothèque.

* `--to <VERSION>`

  Obtenez une version spécifique de la bibliothèque.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

* Pour mettre à jour jQuery vers la dernière version :

  ```console
  libman update jquery
  ```

* Pour mettre à jour jQuery vers la version 3.3.1 :

  ```console
  libman update jquery --to 3.3.1
  ```

* Pour mettre à jour jQuery vers la dernière version préliminaire :

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Gérer le cache de bibliothèque

La `libman cache` commande gère le cache de la bibliothèque LibMan. Le `filesystem` fournisseur n’utilise pas le cache de bibliothèque.

### <a name="synopsis"></a>Synopsis

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Arguments

`PROVIDER`

Utilisé uniquement avec la `clean` commande. Spécifie le cache du fournisseur à nettoyer. Les valeurs valides sont les suivantes :

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `libman cache` :

* `--files`

  Répertoriez les noms des fichiers qui sont mis en cache.

* `--libraries`

  Répertoriez les noms des bibliothèques mises en cache.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Exemples

* Pour afficher les noms des bibliothèques mises en cache par fournisseur, utilisez l’une des commandes suivantes :

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Une sortie similaire à la suivante s’affiche à l’écran :

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* Pour afficher les noms des fichiers de bibliothèque mis en cache par fournisseur :

  ```console
  libman cache list --files
  ```

  Une sortie similaire à la suivante s’affiche à l’écran :

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  Notez que la sortie précédente montre que les versions 3.2.1 et 3.3.1 de jQuery sont mises en cache sous le fournisseur CDNJS.

* Pour vider le cache de bibliothèque pour le fournisseur CDNJS :

  ```console
  libman cache clean cdnjs
  ```

  Une fois le cache du fournisseur CDNJS vidé, la `libman cache list` commande affiche ce qui suit :

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* Pour vider le cache pour tous les fournisseurs pris en charge :

  ```console
  libman cache clean
  ```

  Après avoir vidé tous les caches de fournisseur, la `libman cache list` commande affiche ce qui suit :

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Installer un outil global](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [Dépôt GitHub LibMan](https://github.com/aspnet/LibraryManager)
