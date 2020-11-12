---
title: Tester les API Web avec HttpRepl
author: scottaddie
description: Découvrez comment utiliser l’outil Global .NET Core HttpRepl pour parcourir et tester une API Web ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 11/11/2020
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
uid: web-api/http-repl
ms.openlocfilehash: df2d4e63a18471b4c5f4f1c9434921303bb1da8a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550619"
---
# <a name="test-web-apis-with-the-httprepl"></a>Tester les API Web avec HttpRepl

Par [Scott Addie](https://twitter.com/Scott_Addie)

La boucle REPL (Read-Eval-Print Loop) HTTP est :

* Un outil en ligne de commande léger et multiplateforme qui est pris en charge partout où .NET Core est pris en charge.
* Utilisée pour effectuer des requêtes HTTP pour tester des API web ASP.NET Core (et des API web ASP.NET Core non-ASP.NET) et voir leurs résultats.
* Capable de tester les API web hébergées dans n’importe quel environnement, notamment localhost et Azure App Service.

Les [verbes HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) suivants sont pris en charge :

* [DELETE](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [SIÈGE](#test-http-head-requests)
* [Options](#test-http-options-requests)
* [CORRECTIF](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [PUT](#test-http-put-requests)

Pour continuer, [consultez ou téléchargez l’exemple d’API web ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([comment télécharger](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Prérequis

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>Installation

Pour installer HttpRepl, exécutez la commande suivante :

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

Un [outil global .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) est installé à partir du package NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).

## <a name="usage"></a>Usage

Une fois l’installation de l’outil réussie, exécutez la commande suivante pour démarrer le HttpRepl :

```console
httprepl
```

Pour afficher les commandes HttpRepl disponibles, exécutez l’une des commandes suivantes :

```console
httprepl -h
```

```console
httprepl --help
```

Vous obtenez la sortie suivante :

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

HttpRepl offre la saisie semi-automatique des commandes. Un appui sur la touche <kbd>Tab</kbd> itère au sein de la liste des commandes qui complètent les caractères ou le point de terminaison d’API que vous avez tapés. Les sections suivantes décrivent les commandes CLI disponibles.

## <a name="connect-to-the-web-api"></a>Se connecter à l’API web

Connectez-vous à une API web en exécutant la commande suivante :

```console
httprepl <ROOT URI>
```

`<ROOT URI>` est l’URI de base pour l’API web. Par exemple :

```console
httprepl https://localhost:5001
```

Vous pouvez également exécuter la commande suivante à tout moment pendant l’exécution de HttpRepl :

```console
connect <ROOT URI>
```

Par exemple :

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a>Pointer manuellement vers la description OpenAPI pour l’API Web

La commande Connect ci-dessus tente de trouver la description OpenAPI automatiquement. Si, pour une raison quelconque, il n’est pas possible de le faire, vous pouvez spécifier l’URI de la description OpenAPI pour l’API Web à l’aide de l' `--openapi` option :

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

Par exemple :

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a>Activer la sortie détaillée pour plus d’informations sur OpenAPI Description de la recherche, de l’analyse et de la validation

Si vous spécifiez l' `--verbose` option avec la `connect` commande, vous obtenez plus de détails lorsque l’outil recherche la description openapi, l’analyse et le valide.

```console
connect <ROOT URI> --verbose
```

Par exemple :

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a>Accéder à l’API web

### <a name="view-available-endpoints"></a>Voir les points de terminaison disponibles

Pour lister les différents points de terminaison (contrôleurs) sur le chemin actuel de l’adresse de l’API web, exécutez la commande `ls` ou `dir` :

```console
https://localhot:5001/> ls
```

Le format de sortie suivant s’affiche :

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

La sortie précédente indique que deux contrôleurs sont disponibles : `Fruits` et `People`. Les deux contrôleurs prennent en charge les opérations HTTP GET et POST sans paramètres.

La navigation dans un contrôleur spécifique révèle plus de détails. Par exemple, la sortie de la commande suivante indique que le contrôleur `Fruits` prend également en charge les opérations HTTP GET, PUT et DELETE. Chacune de ces opérations attend un paramètre `id` dans la route :

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

Vous pouvez également exécuter la commande `ui` pour ouvrir la page de l’interface utilisateur Swagger de l’API web dans un navigateur. Par exemple :

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a>Accéder à un point de terminaison

Pour accéder à un autre point de terminaison sur l’API web, exécutez la commande `cd` :

```console
https://localhost:5001/> cd people
```

Le chemin qui suit la commande `cd` ne respecte pas la casse. Le format de sortie suivant s’affiche :

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a>Personnaliser le HttpRepl

Les [couleurs](#set-color-preferences) par défaut de HttpRepl peuvent être personnalisées. Vous pouvez aussi définir un [éditeur de texte par défaut](#set-the-default-text-editor). Les préférences HttpRepl sont conservées dans la session active et sont honorées dans les sessions ultérieures. Une fois modifiées, les préférences sont stockées dans le fichier suivant :

# <a name="linux"></a>[Linux](#tab/linux)

*%HOME%/.httpreplprefs*

# <a name="macos"></a>[MacOS](#tab/macos)

*%HOME%/.httpreplprefs*

# <a name="windows"></a>[Windows](#tab/windows)

*%USERPROFILE%\\.httpreplprefs*

---

Le fichier *.httpreplprefs* est chargé au démarrage et ses modifications ne sont pas surveillées pendant l’exécution. Les modifications manuelles apportées au fichier prennent effet seulement après un redémarrage de l’outil.

### <a name="view-the-settings"></a>Voir les paramètres

Pour voir les paramètres disponibles, exécutez la commande `pref get`. Par exemple :

```console
https://localhost:5001/> pref get
```

La commande précédente affiche les paires clé-valeur disponibles :

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a>Définir les préférences de couleur

La colorisation des réponses est actuellement prise en charge seulement pour JSON. Pour personnaliser les couleurs par défaut de l’outil HttpRepl, localisez la clé correspondant à la couleur à modifier. Pour des instructions sur la façon de trouver les clés, consultez la section [Voir les paramètres](#view-the-settings). Par exemple, remplacez la valeur de la clé `colors.json` de `Green` par `White` :

```console
https://localhost:5001/people> pref set colors.json White
```

Seules les [couleurs autorisées](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) peuvent être utilisées. Les requêtes HTTP suivantes affichent la sortie avec les nouvelles couleurs.

Quand des clés d’une couleur spécifique ne sont pas définies, des clés plus génériques sont prises en compte. Pour illustrer ce comportement de repli, considérez l’exemple suivant :

* Si `colors.json.name` n’a pas de valeur, `colors.json.string` est utilisée.
* Si `colors.json.string` n’a pas de valeur, `colors.json.literal` est utilisée.
* Si `colors.json.literal` n’a pas de valeur, `colors.json` est utilisée. 
* Si `colors.json` n’a pas de valeur, la couleur de texte par défaut de l’interpréteur de commandes (`AllowedColors.None`) est utilisée.

### <a name="set-indentation-size"></a>Définir la taille de la mise en retrait

La personnalisation de la taille de la mise en retrait de la réponse est actuellement prise en charge pour JSON uniquement. La taille par défaut est de deux espaces. Par exemple :

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

Pour changer la taille par défaut, définissez la clé `formatting.json.indentSize`. Par exemple, pour toujours utiliser quatre espaces :

```console
pref set formatting.json.indentSize 4
```

Les réponses suivantes adoptent la valeur correspondant à quatre espaces :

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a>Définir l’éditeur de texte par défaut

Par défaut, le HttpRepl n’a pas d’éditeur de texte configuré pour être utilisé. Pour tester les méthodes d’API web nécessitant un corps de requête HTTP, vous devez définir un éditeur de texte par défaut. L’outil HttpRepl lance l’éditeur de texte configuré dans le seul but de composer le corps de la requête. Exécutez la commande suivante pour définir votre éditeur de texte préféré comme éditeur par défaut :

```console
pref set editor.command.default "<EXECUTABLE>"
```

Dans la commande précédente, `<EXECUTABLE>` est le chemin complet du fichier exécutable de l’éditeur de texte. Par exemple, exécutez la commande suivante pour définir Visual Studio Code comme éditeur de texte par défaut :

# <a name="linux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

Pour lancer l’éditeur de texte par défaut avec des arguments CLI spécifiques, définissez la clé `editor.command.default.arguments`. Par exemple, supposons que Visual Studio Code est l’éditeur de texte par défaut et que vous souhaitez toujours que le HttpRepl ouvre Visual Studio Code dans une nouvelle session avec les extensions désactivées. Exécutez la commande suivante :

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> Si votre éditeur par défaut est Visual Studio Code, vous souhaiterez généralement passer l' `-w` `--wait` argument ou pour forcer Visual Studio code à attendre que vous fermiez le fichier avant de retourner.

### <a name="set-the-openapi-description-search-paths"></a>Définir les chemins de recherche de la description OpenAPI

Par défaut, le HttpRepl possède un ensemble de chemins d’accès relatifs qu’il utilise pour trouver la description de OpenAPI lors de l’exécution de la `connect` commande sans l' `--openapi` option. Ces chemins relatifs sont combinés avec les chemins racine et de base spécifiés dans la commande `connect`. Les chemins relatifs par défaut sont les suivants :

- *swagger.js*
- *Swagger/v1/swagger.jssur*
- */swagger.jssur*
- */swagger/v1/swagger.json*
- *openapi.js*
- */openapi.jssur*

Pour utiliser un autre ensemble de chemins de recherche dans votre environnement, définissez la préférence `swagger.searchPaths`. La valeur doit être une liste de chemins relatifs délimités par des barres verticales. Par exemple :

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

Au lieu de remplacer entièrement la liste par défaut, la liste peut également être modifiée en ajoutant ou en supprimant des chemins.

Pour ajouter un ou plusieurs chemins de recherche à la liste par défaut, définissez la `swagger.addToSearchPaths` préférence. La valeur doit être une liste de chemins relatifs délimités par des barres verticales. Par exemple :

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

Pour supprimer un ou plusieurs chemins de recherche de la liste par défaut, définissez la `swagger.addToSearchPaths` préférence. La valeur doit être une liste de chemins relatifs délimités par des barres verticales. Par exemple :

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a>Tester des requêtes HTTP GET

### <a name="synopsis"></a>Synopsis

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.

### <a name="options"></a>Options

Les options suivantes sont disponibles pour la commande `get` :

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Exemple

Pour émettre une requête HTTP GET :

1. Exécutez la commande `get` sur un point de terminaison qui la prend en charge :

    ```console
    https://localhost:5001/people> get
    ```

    La commande précédente affiche le format de sortie suivant :

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people>
    ```

1. Récupérez un enregistrement spécifique en passant un paramètre à la commande `get` :

    ```console
    https://localhost:5001/people> get 2
    ```

    La commande précédente affiche le format de sortie suivant :

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a>Tester des requêtes HTTP POST

### <a name="synopsis"></a>Synopsis

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.

### <a name="options"></a>Options

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Exemple

Pour émettre une requête HTTP POST :

1. Exécutez la commande `post` sur un point de terminaison qui la prend en charge :

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    Dans la commande précédente, l’en-tête `Content-Type` de la requête HTTP est défini pour indiquer un type de média de corps de requête JSON. L’éditeur de texte par défaut ouvre un fichier *.tmp* avec un modèle JSON représentant le corps de la requête HTTP. Par exemple :

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Pour définir l’éditeur de texte par défaut, consultez la section [Définir l’éditeur de texte par défaut](#set-the-default-text-editor).

1. Modifiez le modèle JSON pour répondre aux exigences de validation du modèle :

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. Enregistrez le fichier *.tmp* et fermez l’éditeur de texte. La sortie suivante s’affiche dans l’interpréteur de commandes :

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a>Tester des requêtes HTTP PUT

### <a name="synopsis"></a>Synopsis

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.

### <a name="options"></a>Options

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Exemple

Pour émettre une requête HTTP PUT :

1. *Facultatif* : exécutez la `get` commande pour afficher les données avant de les modifier :

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. Exécutez la commande `put` sur un point de terminaison qui la prend en charge :

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    Dans la commande précédente, l’en-tête `Content-Type` de la requête HTTP est défini pour indiquer un type de média de corps de requête JSON. L’éditeur de texte par défaut ouvre un fichier *.tmp* avec un modèle JSON représentant le corps de la requête HTTP. Par exemple :

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Pour définir l’éditeur de texte par défaut, consultez la section [Définir l’éditeur de texte par défaut](#set-the-default-text-editor).

1. Modifiez le modèle JSON pour répondre aux exigences de validation du modèle :

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. Enregistrez le fichier *.tmp* et fermez l’éditeur de texte. La sortie suivante s’affiche dans l’interpréteur de commandes :

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *Facultatif* : émettez une `get` commande pour voir les modifications. Par exemple, si vous avez tapé « cerise » dans l’éditeur de texte, un `get` retourne la sortie suivante :

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a>Tester des requêtes HTTP DELETE

### <a name="synopsis"></a>Synopsis

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.

### <a name="options"></a>Options

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Exemple

Pour émettre une requête HTTP DELETE :

1. *Facultatif* : exécutez la `get` commande pour afficher les données avant de les modifier :

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. Exécutez la commande `delete` sur un point de terminaison qui la prend en charge :

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    La commande précédente affiche le format de sortie suivant :

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *Facultatif* : émettez une `get` commande pour voir les modifications. Dans cet exemple, un `get` retourne la sortie suivante :

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a>Tester des requêtes HTTP PATCH

### <a name="synopsis"></a>Synopsis

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.

### <a name="options"></a>Options

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>Tester des requêtes HTTP HEAD

### <a name="synopsis"></a>Synopsis

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.

### <a name="options"></a>Options

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>Tester des requêtes HTTP OPTIONS

### <a name="synopsis"></a>Synopsis

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.

### <a name="options"></a>Options

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>Définir des en-têtes de requête HTTP

Pour définir un en-tête de requête HTTP, utilisez une des approches suivantes :

* Définir inline avec la requête HTTP. Par exemple :

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    Avec l’approche précédente, chaque en-tête de requête HTTP distinct nécessite sa propre option `-h`.

* Définir avant l’envoi de la requête HTTP. Par exemple :

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    Si l’en-tête est défini avant l’envoi d’une requête, l’en-tête reste défini pour la durée de la session de l’interpréteur de commandes. Pour effacer l’en-tête, spécifiez une valeur vide. Par exemple :
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a>Tester les points de terminaison sécurisés

Le HttpRepl prend en charge le test des points de terminaison sécurisés des manières suivantes :

* Par le biais des informations d’identification par défaut de l’utilisateur connecté.
* À l’aide des en-têtes de requête HTTP.

### <a name="default-credentials"></a>Informations d’identification par défaut

Prenons l’exemple d’une API Web que vous testez et qui est hébergée dans IIS et sécurisée avec l’authentification Windows. Vous souhaitez que les informations d’identification de l’utilisateur qui exécute l’outil soient transmises aux points de terminaison HTTP en cours de test. Pour transmettre les informations d’identification par défaut de l’utilisateur connecté :

1. Définissez la `httpClient.useDefaultCredentials` préférence sur `true` :

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. Quittez et redémarrez l’outil avant d’envoyer une autre demande à l’API Web.
 
### <a name="default-proxy-credentials"></a>Informations d’identification du proxy par défaut

Imaginez un scénario dans lequel l’API Web que vous testez se trouve derrière un proxy sécurisé avec l’authentification Windows. Vous souhaitez que les informations d’identification de l’utilisateur qui exécute l’outil soient acheminées vers le proxy. Pour transmettre les informations d’identification par défaut de l’utilisateur connecté :

1. Définissez la `httpClient.proxy.useDefaultCredentials` préférence sur `true` :

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. Quittez et redémarrez l’outil avant d’envoyer une autre demande à l’API Web.

### <a name="http-request-headers"></a>En-têtes de requête HTTP

Voici quelques exemples de modèles d’authentification et d’autorisation pris en charge :

* basic authentication
* Jetons du porteur JWT
* authentification Digest

Par exemple, vous pouvez envoyer un jeton de porteur à un point de terminaison à l’aide de la commande suivante :

```console
set header Authorization "bearer <TOKEN VALUE>"
```

Pour accéder à un point de terminaison hébergé par Azure ou pour utiliser l' [API REST Azure](/rest/api/azure/), vous avez besoin d’un jeton de porteur. Procédez comme suit pour obtenir un jeton de porteur pour votre abonnement Azure via le [Azure CLI](/cli/azure/). HttpRepl définit le jeton du porteur dans un en-tête de requête HTTP. Une liste de Azure App Service Web Apps est récupérée.

1. Connectez-vous à Azure :

    ```azurecli
    az login
    ```

1. Récupérez votre ID d’abonnement à l’aide de la commande suivante :

    ```azurecli
    az account show --query id
    ```

1. Copiez votre ID d’abonnement et exécutez la commande suivante :

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. Récupérez votre jeton de porteur avec la commande suivante :

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. Connectez-vous à l’API REST Azure via HttpRepl :

    ```console
    httprepl https://management.azure.com
    ```

1. Définissez l' `Authorization` en-tête de requête http :

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. Accédez à l’abonnement :

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. Obtenir la liste des Web Apps Azure App Service de votre abonnement :

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    La réponse suivante s’affiche :

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a>Activer/désactiver l’affichage des requêtes HTTP

Par défaut, l’affichage de la requête HTTP envoyée est supprimé. Il est possible de changer le paramètre correspondant pour la durée de la session de l’interpréteur de commandes.

### <a name="enable-request-display"></a>Activer l’affichage des requêtes

Affichez la requête HTTP envoyée en exécutant la commande `echo on`. Par exemple :

```console
https://localhost:5001/people> echo on
Request echoing is on
```

Les requêtes HTTP suivantes dans la session active affichent les en-têtes de requête. Par exemple :

```console
https://localhost:5001/people> post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people>
```

### <a name="disable-request-display"></a>Désactiver l’affichage des requêtes

Supprimez l’affichage de la requête HTTP envoyée en exécutant la commande `echo off`. Par exemple :

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a>Exécuter un script

Si vous exécutez fréquemment le même jeu de commandes HttpRepl, envisagez de les stocker dans un fichier texte. Les commandes du fichier prennent la même forme que les commandes exécutées manuellement sur la ligne de commande. Les commandes peuvent être exécutées de façon groupée avec la commande `run`. Par exemple :

1. Créez un fichier texte contenant un ensemble de commandes délimitées par des sauts de ligne. Pour illustrer ceci, considérez un fichier *people-script.txt* contenant les commandes suivantes :

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. Exécutez la commande `run`, en passant le chemin du fichier texte. Par exemple :

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    Vous obtenez la sortie suivante :

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a>Effacer la sortie

Pour supprimer toutes les sorties écrites dans l’interface de commande par l’outil HttpRepl, exécutez la `clear` `cls` commande ou. À titre d’illustration, imaginez que l’interpréteur de commandes contient la sortie suivante :

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

Exécutez la commande suivante pour effacer la sortie :

```console
https://localhost:5001/> clear
```

Après l’exécution de la commande précédente, l’interpréteur de commandes contient seulement la sortie suivante :

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Requêtes de l’API REST](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [Référentiel GitHub HttpRepl](https://github.com/dotnet/HttpRepl)
