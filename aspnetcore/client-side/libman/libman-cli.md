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
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="26798-103">Utiliser l’interface CLI LibMan avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26798-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="26798-104">Par [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="26798-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="26798-105">L’interface CLI [LibMan](xref:client-side/libman/index) est un outil multiplateforme qui est pris en charge partout où .net Core est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="26798-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="26798-106">Conditions préalables requises</span><span class="sxs-lookup"><span data-stu-id="26798-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="26798-107">Installation</span><span class="sxs-lookup"><span data-stu-id="26798-107">Installation</span></span>

<span data-ttu-id="26798-108">Pour installer l’interface CLI LibMan :</span><span class="sxs-lookup"><span data-stu-id="26798-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="26798-109">Un [outil Global .net Core](/dotnet/core/tools/global-tools#install-a-global-tool) est installé à partir du package NuGet [Microsoft. Web. librarymanager. CLI](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) .</span><span class="sxs-lookup"><span data-stu-id="26798-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="26798-110">Pour installer l’interface CLI LibMan à partir d’une source de package NuGet spécifique :</span><span class="sxs-lookup"><span data-stu-id="26798-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="26798-111">Dans l’exemple précédent, un outil Global .NET Core est installé à partir du fichier *C:\Temp\Microsoft.Web.librarymanager.cli.1.0.94-g606058a278.nupkg* de l’ordinateur Windows local.</span><span class="sxs-lookup"><span data-stu-id="26798-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="26798-112">Usage</span><span class="sxs-lookup"><span data-stu-id="26798-112">Usage</span></span>

<span data-ttu-id="26798-113">Une fois l’installation de l’interface CLI réussie, vous pouvez utiliser la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="26798-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="26798-114">Pour afficher la version installée de l’interface de commande :</span><span class="sxs-lookup"><span data-stu-id="26798-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="26798-115">Pour afficher les commandes CLI disponibles :</span><span class="sxs-lookup"><span data-stu-id="26798-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="26798-116">La commande précédente affiche une sortie similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="26798-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="26798-117">Les sections suivantes décrivent les commandes CLI disponibles.</span><span class="sxs-lookup"><span data-stu-id="26798-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="26798-118">Initialiser LibMan dans le projet</span><span class="sxs-lookup"><span data-stu-id="26798-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="26798-119">La `libman init` commande crée un *libman.jssur* le fichier s’il n’en existe pas.</span><span class="sxs-lookup"><span data-stu-id="26798-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="26798-120">Le fichier est créé avec le contenu du modèle d’élément par défaut.</span><span class="sxs-lookup"><span data-stu-id="26798-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="26798-121">Synopsis</span><span class="sxs-lookup"><span data-stu-id="26798-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="26798-122">Options</span><span class="sxs-lookup"><span data-stu-id="26798-122">Options</span></span>

<span data-ttu-id="26798-123">Les options suivantes sont disponibles pour la commande `libman init` :</span><span class="sxs-lookup"><span data-stu-id="26798-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="26798-124">Chemin d’accès relatif au dossier actif.</span><span class="sxs-lookup"><span data-stu-id="26798-124">A path relative to the current folder.</span></span> <span data-ttu-id="26798-125">Les fichiers de bibliothèque sont installés à cet emplacement si aucune `destination` propriété n’est définie pour une bibliothèque dans *libman.jssur*.</span><span class="sxs-lookup"><span data-stu-id="26798-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="26798-126">La `<PATH>` valeur est écrite dans la `defaultDestination` propriété de *libman.js*.</span><span class="sxs-lookup"><span data-stu-id="26798-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="26798-127">Fournisseur à utiliser si aucun fournisseur n’est défini pour une bibliothèque donnée.</span><span class="sxs-lookup"><span data-stu-id="26798-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="26798-128">La `<PROVIDER>` valeur est écrite dans la `defaultProvider` propriété de *libman.js*.</span><span class="sxs-lookup"><span data-stu-id="26798-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="26798-129">Remplacez `<PROVIDER>` par l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="26798-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="26798-130">Exemples</span><span class="sxs-lookup"><span data-stu-id="26798-130">Examples</span></span>

<span data-ttu-id="26798-131">Pour créer un *libman.jssur* un fichier dans un projet ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="26798-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="26798-132">Accédez à la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="26798-132">Navigate to the project root.</span></span>
* <span data-ttu-id="26798-133">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="26798-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="26798-134">Tapez le nom du fournisseur par défaut ou appuyez sur `Enter` pour utiliser le fournisseur CDNJS par défaut.</span><span class="sxs-lookup"><span data-stu-id="26798-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="26798-135">Les valeurs valides sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="26798-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![commande Libman init-fournisseur par défaut](_static/libman-init-provider.png)

<span data-ttu-id="26798-137">Un *libman.jssur* le fichier est ajouté à la racine du projet avec le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="26798-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="26798-138">Ajouter des fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="26798-138">Add library files</span></span>

<span data-ttu-id="26798-139">La `libman install` commande télécharge et installe les fichiers de bibliothèque dans le projet.</span><span class="sxs-lookup"><span data-stu-id="26798-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="26798-140">Un *libman.jssur le* fichier est ajouté s’il n’en existe pas.</span><span class="sxs-lookup"><span data-stu-id="26798-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="26798-141">La *libman.jssur* le fichier est modifiée pour stocker les détails de configuration des fichiers de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="26798-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="26798-142">Synopsis</span><span class="sxs-lookup"><span data-stu-id="26798-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="26798-143">Arguments</span><span class="sxs-lookup"><span data-stu-id="26798-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="26798-144">Nom de la bibliothèque à installer.</span><span class="sxs-lookup"><span data-stu-id="26798-144">The name of the library to install.</span></span> <span data-ttu-id="26798-145">Ce nom peut inclure la notation du numéro de version (par exemple, `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="26798-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="26798-146">Options</span><span class="sxs-lookup"><span data-stu-id="26798-146">Options</span></span>

<span data-ttu-id="26798-147">Les options suivantes sont disponibles pour la commande `libman install` :</span><span class="sxs-lookup"><span data-stu-id="26798-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="26798-148">Emplacement d’installation de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="26798-148">The location to install the library.</span></span> <span data-ttu-id="26798-149">S’il n’est pas spécifié, l’emplacement par défaut est utilisé.</span><span class="sxs-lookup"><span data-stu-id="26798-149">If not specified, the default location is used.</span></span> <span data-ttu-id="26798-150">Si aucune `defaultDestination` propriété n’est spécifiée dans *libman.jssur*, cette option est obligatoire.</span><span class="sxs-lookup"><span data-stu-id="26798-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="26798-151">Spécifiez le nom du fichier à installer à partir de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="26798-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="26798-152">S’il n’est pas spécifié, tous les fichiers de la bibliothèque sont installés.</span><span class="sxs-lookup"><span data-stu-id="26798-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="26798-153">Fournissez une `--files` option par fichier à installer.</span><span class="sxs-lookup"><span data-stu-id="26798-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="26798-154">Les chemins d’accès relatifs sont également pris en charge.</span><span class="sxs-lookup"><span data-stu-id="26798-154">Relative paths are supported too.</span></span> <span data-ttu-id="26798-155">Par exemple : `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="26798-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="26798-156">Nom du fournisseur à utiliser pour l’acquisition de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="26798-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="26798-157">Remplacez `<PROVIDER>` par l’une des valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="26798-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="26798-158">S’il n’est pas spécifié, la `defaultProvider` propriété de *libman.jssur* est utilisée.</span><span class="sxs-lookup"><span data-stu-id="26798-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="26798-159">Si aucune `defaultProvider` propriété n’est spécifiée dans *libman.jssur*, cette option est obligatoire.</span><span class="sxs-lookup"><span data-stu-id="26798-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="26798-160">Exemples</span><span class="sxs-lookup"><span data-stu-id="26798-160">Examples</span></span>

<span data-ttu-id="26798-161">Prenez en compte les *libman.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="26798-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="26798-162">Pour installer le fichier de *jquery.min.js* jQuery version 3.2.1 dans le dossier *wwwroot/scripts/jQuery* à l’aide du fournisseur CDNJS :</span><span class="sxs-lookup"><span data-stu-id="26798-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="26798-163">Le *libman.jssur* le fichier ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="26798-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="26798-164">Pour installer les fichiers *calendar.js* et *Calendar. CSS* à partir de *C : \\ temp \\ contosoCalendar \\* à l’aide du fournisseur de système de fichiers :</span><span class="sxs-lookup"><span data-stu-id="26798-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="26798-165">L’invite suivante s’affiche pour deux raisons :</span><span class="sxs-lookup"><span data-stu-id="26798-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="26798-166">Le *libman.jssur* le fichier ne contient pas de `defaultDestination` propriété.</span><span class="sxs-lookup"><span data-stu-id="26798-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="26798-167">La `libman install` commande ne contient pas l' `-d|--destination` option.</span><span class="sxs-lookup"><span data-stu-id="26798-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![commande d’installation de Libman-destination](_static/libman-install-destination.png)

<span data-ttu-id="26798-169">Après avoir accepté la destination par défaut, le *libman.jssur* le fichier ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="26798-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="26798-170">Restaurer les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="26798-170">Restore library files</span></span>

<span data-ttu-id="26798-171">La `libman restore` commande installe les fichiers de bibliothèque définis dans *libman.jssur*.</span><span class="sxs-lookup"><span data-stu-id="26798-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="26798-172">Les règles suivantes s’appliquent :</span><span class="sxs-lookup"><span data-stu-id="26798-172">The following rules apply:</span></span>

* <span data-ttu-id="26798-173">S’il n’existe aucun *libman.jssur* le fichier dans la racine du projet, une erreur est retournée.</span><span class="sxs-lookup"><span data-stu-id="26798-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="26798-174">Si une bibliothèque spécifie un fournisseur, la `defaultProvider` propriété de *libman.jssur* est ignorée.</span><span class="sxs-lookup"><span data-stu-id="26798-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="26798-175">Si une bibliothèque spécifie une destination, la `defaultDestination` propriété de *libman.jssur* est ignorée.</span><span class="sxs-lookup"><span data-stu-id="26798-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="26798-176">Synopsis</span><span class="sxs-lookup"><span data-stu-id="26798-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="26798-177">Options</span><span class="sxs-lookup"><span data-stu-id="26798-177">Options</span></span>

<span data-ttu-id="26798-178">Les options suivantes sont disponibles pour la commande `libman restore` :</span><span class="sxs-lookup"><span data-stu-id="26798-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="26798-179">Exemples</span><span class="sxs-lookup"><span data-stu-id="26798-179">Examples</span></span>

<span data-ttu-id="26798-180">Pour restaurer les fichiers de bibliothèque définis dans *libman.jssur*:</span><span class="sxs-lookup"><span data-stu-id="26798-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="26798-181">Supprimer les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="26798-181">Delete library files</span></span>

<span data-ttu-id="26798-182">La `libman clean` commande supprime les fichiers de bibliothèque précédemment restaurés via LibMan.</span><span class="sxs-lookup"><span data-stu-id="26798-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="26798-183">Dossiers qui deviennent vides après la suppression de cette opération.</span><span class="sxs-lookup"><span data-stu-id="26798-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="26798-184">Les configurations associées aux fichiers de bibliothèque dans la `libraries` propriété de *libman.jssur* ne sont pas supprimées.</span><span class="sxs-lookup"><span data-stu-id="26798-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="26798-185">Synopsis</span><span class="sxs-lookup"><span data-stu-id="26798-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="26798-186">Options</span><span class="sxs-lookup"><span data-stu-id="26798-186">Options</span></span>

<span data-ttu-id="26798-187">Les options suivantes sont disponibles pour la commande `libman clean` :</span><span class="sxs-lookup"><span data-stu-id="26798-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="26798-188">Exemples</span><span class="sxs-lookup"><span data-stu-id="26798-188">Examples</span></span>

<span data-ttu-id="26798-189">Pour supprimer les fichiers de bibliothèque installés via LibMan :</span><span class="sxs-lookup"><span data-stu-id="26798-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="26798-190">Désinstaller les fichiers de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="26798-190">Uninstall library files</span></span>

<span data-ttu-id="26798-191">`libman uninstall`Commande :</span><span class="sxs-lookup"><span data-stu-id="26798-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="26798-192">Supprime de la destination tous les fichiers associés à la bibliothèque spécifiée dans *libman.js*.</span><span class="sxs-lookup"><span data-stu-id="26798-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="26798-193">Supprime la configuration de bibliothèque associée de *libman.js*.</span><span class="sxs-lookup"><span data-stu-id="26798-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="26798-194">Une erreur se produit dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="26798-194">An error occurs when:</span></span>

* <span data-ttu-id="26798-195">Il n’existe aucun *libman.jssur* le fichier dans la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="26798-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="26798-196">La bibliothèque spécifiée n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="26798-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="26798-197">Si plusieurs bibliothèques portant le même nom sont installées, vous êtes invité à en choisir une.</span><span class="sxs-lookup"><span data-stu-id="26798-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="26798-198">Synopsis</span><span class="sxs-lookup"><span data-stu-id="26798-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="26798-199">Arguments</span><span class="sxs-lookup"><span data-stu-id="26798-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="26798-200">Nom de la bibliothèque à désinstaller.</span><span class="sxs-lookup"><span data-stu-id="26798-200">The name of the library to uninstall.</span></span> <span data-ttu-id="26798-201">Ce nom peut inclure la notation du numéro de version (par exemple, `@1.2.0` ).</span><span class="sxs-lookup"><span data-stu-id="26798-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="26798-202">Options</span><span class="sxs-lookup"><span data-stu-id="26798-202">Options</span></span>

<span data-ttu-id="26798-203">Les options suivantes sont disponibles pour la commande `libman uninstall` :</span><span class="sxs-lookup"><span data-stu-id="26798-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="26798-204">Exemples</span><span class="sxs-lookup"><span data-stu-id="26798-204">Examples</span></span>

<span data-ttu-id="26798-205">Prenez en compte les *libman.jssuivantes sur* le fichier :</span><span class="sxs-lookup"><span data-stu-id="26798-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="26798-206">Pour désinstaller jQuery, l’une des commandes suivantes est réussie :</span><span class="sxs-lookup"><span data-stu-id="26798-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="26798-207">Pour désinstaller les fichiers Lodash installés via le `filesystem` fournisseur :</span><span class="sxs-lookup"><span data-stu-id="26798-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="26798-208">Version de la bibliothèque de mises à jour</span><span class="sxs-lookup"><span data-stu-id="26798-208">Update library version</span></span>

<span data-ttu-id="26798-209">La `libman update` commande met à jour une bibliothèque installée via LibMan avec la version spécifiée.</span><span class="sxs-lookup"><span data-stu-id="26798-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="26798-210">Une erreur se produit dans les cas suivants :</span><span class="sxs-lookup"><span data-stu-id="26798-210">An error occurs when:</span></span>

* <span data-ttu-id="26798-211">Il n’existe aucun *libman.jssur* le fichier dans la racine du projet.</span><span class="sxs-lookup"><span data-stu-id="26798-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="26798-212">La bibliothèque spécifiée n’existe pas.</span><span class="sxs-lookup"><span data-stu-id="26798-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="26798-213">Si plusieurs bibliothèques portant le même nom sont installées, vous êtes invité à en choisir une.</span><span class="sxs-lookup"><span data-stu-id="26798-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="26798-214">Synopsis</span><span class="sxs-lookup"><span data-stu-id="26798-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="26798-215">Arguments</span><span class="sxs-lookup"><span data-stu-id="26798-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="26798-216">Nom de la bibliothèque à mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="26798-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="26798-217">Options</span><span class="sxs-lookup"><span data-stu-id="26798-217">Options</span></span>

<span data-ttu-id="26798-218">Les options suivantes sont disponibles pour la commande `libman update` :</span><span class="sxs-lookup"><span data-stu-id="26798-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="26798-219">Obtenez la dernière version préliminaire de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="26798-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="26798-220">Obtenez une version spécifique de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="26798-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="26798-221">Exemples</span><span class="sxs-lookup"><span data-stu-id="26798-221">Examples</span></span>

* <span data-ttu-id="26798-222">Pour mettre à jour jQuery vers la dernière version :</span><span class="sxs-lookup"><span data-stu-id="26798-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="26798-223">Pour mettre à jour jQuery vers la version 3.3.1 :</span><span class="sxs-lookup"><span data-stu-id="26798-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="26798-224">Pour mettre à jour jQuery vers la dernière version préliminaire :</span><span class="sxs-lookup"><span data-stu-id="26798-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="26798-225">Gérer le cache de bibliothèque</span><span class="sxs-lookup"><span data-stu-id="26798-225">Manage library cache</span></span>

<span data-ttu-id="26798-226">La `libman cache` commande gère le cache de la bibliothèque LibMan.</span><span class="sxs-lookup"><span data-stu-id="26798-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="26798-227">Le `filesystem` fournisseur n’utilise pas le cache de bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="26798-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="26798-228">Synopsis</span><span class="sxs-lookup"><span data-stu-id="26798-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="26798-229">Arguments</span><span class="sxs-lookup"><span data-stu-id="26798-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="26798-230">Utilisé uniquement avec la `clean` commande.</span><span class="sxs-lookup"><span data-stu-id="26798-230">Only used with the `clean` command.</span></span> <span data-ttu-id="26798-231">Spécifie le cache du fournisseur à nettoyer.</span><span class="sxs-lookup"><span data-stu-id="26798-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="26798-232">Les valeurs valides sont les suivantes :</span><span class="sxs-lookup"><span data-stu-id="26798-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="26798-233">Options</span><span class="sxs-lookup"><span data-stu-id="26798-233">Options</span></span>

<span data-ttu-id="26798-234">Les options suivantes sont disponibles pour la commande `libman cache` :</span><span class="sxs-lookup"><span data-stu-id="26798-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="26798-235">Répertoriez les noms des fichiers qui sont mis en cache.</span><span class="sxs-lookup"><span data-stu-id="26798-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="26798-236">Répertoriez les noms des bibliothèques mises en cache.</span><span class="sxs-lookup"><span data-stu-id="26798-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="26798-237">Exemples</span><span class="sxs-lookup"><span data-stu-id="26798-237">Examples</span></span>

* <span data-ttu-id="26798-238">Pour afficher les noms des bibliothèques mises en cache par fournisseur, utilisez l’une des commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="26798-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="26798-239">Une sortie similaire à la suivante s’affiche à l’écran :</span><span class="sxs-lookup"><span data-stu-id="26798-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="26798-240">Pour afficher les noms des fichiers de bibliothèque mis en cache par fournisseur :</span><span class="sxs-lookup"><span data-stu-id="26798-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="26798-241">Une sortie similaire à la suivante s’affiche à l’écran :</span><span class="sxs-lookup"><span data-stu-id="26798-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="26798-242">Notez que la sortie précédente montre que les versions 3.2.1 et 3.3.1 de jQuery sont mises en cache sous le fournisseur CDNJS.</span><span class="sxs-lookup"><span data-stu-id="26798-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="26798-243">Pour vider le cache de bibliothèque pour le fournisseur CDNJS :</span><span class="sxs-lookup"><span data-stu-id="26798-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="26798-244">Une fois le cache du fournisseur CDNJS vidé, la `libman cache list` commande affiche ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="26798-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="26798-245">Pour vider le cache pour tous les fournisseurs pris en charge :</span><span class="sxs-lookup"><span data-stu-id="26798-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="26798-246">Après avoir vidé tous les caches de fournisseur, la `libman cache list` commande affiche ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="26798-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="26798-247">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="26798-247">Additional resources</span></span>

* [<span data-ttu-id="26798-248">Installer un outil global</span><span class="sxs-lookup"><span data-stu-id="26798-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="26798-249">Dépôt GitHub LibMan</span><span class="sxs-lookup"><span data-stu-id="26798-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
