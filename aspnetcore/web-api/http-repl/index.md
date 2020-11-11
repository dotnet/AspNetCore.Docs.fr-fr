---
title: Tester des API web avec la boucle REPL HTTP
author: scottaddie
description: Découvrez comment utiliser l’outil global REPL HTTP de .NET Core pour parcourir et tester une API web ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 11/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/http-repl
ms.openlocfilehash: 81174b551c5b6d81e6ac80975f7f77ee6664059d
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502033"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="a0bbd-103">Tester des API web avec la boucle REPL HTTP</span><span class="sxs-lookup"><span data-stu-id="a0bbd-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="a0bbd-104">Par [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a0bbd-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a0bbd-105">La boucle REPL (Read-Eval-Print Loop) HTTP est :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="a0bbd-106">Un outil en ligne de commande léger et multiplateforme qui est pris en charge partout où .NET Core est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="a0bbd-107">Utilisée pour effectuer des requêtes HTTP pour tester des API web ASP.NET Core (et des API web ASP.NET Core non-ASP.NET) et voir leurs résultats.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="a0bbd-108">Capable de tester les API web hébergées dans n’importe quel environnement, notamment localhost et Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="a0bbd-109">Les [verbes HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) suivants sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="a0bbd-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="a0bbd-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="a0bbd-111">GET</span><span class="sxs-lookup"><span data-stu-id="a0bbd-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="a0bbd-112">SIÈGE</span><span class="sxs-lookup"><span data-stu-id="a0bbd-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="a0bbd-113">Options</span><span class="sxs-lookup"><span data-stu-id="a0bbd-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="a0bbd-114">CORRECTIF</span><span class="sxs-lookup"><span data-stu-id="a0bbd-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="a0bbd-115">POST</span><span class="sxs-lookup"><span data-stu-id="a0bbd-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="a0bbd-116">PUT</span><span class="sxs-lookup"><span data-stu-id="a0bbd-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="a0bbd-117">Pour continuer, [consultez ou téléchargez l’exemple d’API web ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a0bbd-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a0bbd-118">Prérequis</span><span class="sxs-lookup"><span data-stu-id="a0bbd-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="a0bbd-119">Installation</span><span class="sxs-lookup"><span data-stu-id="a0bbd-119">Installation</span></span>

<span data-ttu-id="a0bbd-120">Pour installer la boucle REPL HTTP, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="a0bbd-121">Un [outil global .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) est installé à partir du package NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).</span><span class="sxs-lookup"><span data-stu-id="a0bbd-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="a0bbd-122">Usage</span><span class="sxs-lookup"><span data-stu-id="a0bbd-122">Usage</span></span>

<span data-ttu-id="a0bbd-123">Une fois l’installation de l’outil réussie, exécutez la commande suivante pour démarrer la boucle REPL HTTP :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="a0bbd-124">Pour voir les commandes de la boucle REPL HTTP disponibles, exécutez une des commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="a0bbd-125">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-125">The following output is displayed:</span></span>

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

<span data-ttu-id="a0bbd-126">La boucle REPL HTTP offre la complétion des commandes.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="a0bbd-127">Un appui sur la touche <kbd>Tab</kbd> itère au sein de la liste des commandes qui complètent les caractères ou le point de terminaison d’API que vous avez tapés.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="a0bbd-128">Les sections suivantes décrivent les commandes CLI disponibles.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="a0bbd-129">Se connecter à l’API web</span><span class="sxs-lookup"><span data-stu-id="a0bbd-129">Connect to the web API</span></span>

<span data-ttu-id="a0bbd-130">Connectez-vous à une API web en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="a0bbd-131">`<ROOT URI>` est l’URI de base pour l’API web.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="a0bbd-132">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="a0bbd-133">Vous pouvez également exécuter la commande suivante à tout moment pendant l’exécution de la boucle REPL HTTP :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="a0bbd-134">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="a0bbd-135">Pointer manuellement vers la description OpenAPI pour l’API Web</span><span class="sxs-lookup"><span data-stu-id="a0bbd-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="a0bbd-136">La commande Connect ci-dessus tente de trouver la description OpenAPI automatiquement.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="a0bbd-137">Si, pour une raison quelconque, il n’est pas possible de le faire, vous pouvez spécifier l’URI de la description OpenAPI pour l’API Web à l’aide de l' `--openapi` option :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="a0bbd-138">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="a0bbd-139">Activer la sortie détaillée pour plus d’informations sur OpenAPI Description de la recherche, de l’analyse et de la validation</span><span class="sxs-lookup"><span data-stu-id="a0bbd-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="a0bbd-140">Si vous spécifiez l' `--verbose` option avec la `connect` commande, vous obtenez plus de détails lorsque l’outil recherche la description openapi, l’analyse et le valide.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="a0bbd-141">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="a0bbd-142">Accéder à l’API web</span><span class="sxs-lookup"><span data-stu-id="a0bbd-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="a0bbd-143">Voir les points de terminaison disponibles</span><span class="sxs-lookup"><span data-stu-id="a0bbd-143">View available endpoints</span></span>

<span data-ttu-id="a0bbd-144">Pour lister les différents points de terminaison (contrôleurs) sur le chemin actuel de l’adresse de l’API web, exécutez la commande `ls` ou `dir` :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/> ls
```

<span data-ttu-id="a0bbd-145">Le format de sortie suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="a0bbd-146">La sortie précédente indique que deux contrôleurs sont disponibles : `Fruits` et `People`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="a0bbd-147">Les deux contrôleurs prennent en charge les opérations HTTP GET et POST sans paramètres.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="a0bbd-148">La navigation dans un contrôleur spécifique révèle plus de détails.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="a0bbd-149">Par exemple, la sortie de la commande suivante indique que le contrôleur `Fruits` prend également en charge les opérations HTTP GET, PUT et DELETE.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="a0bbd-150">Chacune de ces opérations attend un paramètre `id` dans la route :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="a0bbd-151">Vous pouvez également exécuter la commande `ui` pour ouvrir la page de l’interface utilisateur Swagger de l’API web dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="a0bbd-152">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="a0bbd-153">Accéder à un point de terminaison</span><span class="sxs-lookup"><span data-stu-id="a0bbd-153">Navigate to an endpoint</span></span>

<span data-ttu-id="a0bbd-154">Pour accéder à un autre point de terminaison sur l’API web, exécutez la commande `cd` :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="a0bbd-155">Le chemin qui suit la commande `cd` ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="a0bbd-156">Le format de sortie suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="a0bbd-157">Personnaliser la boucle REPL HTTP</span><span class="sxs-lookup"><span data-stu-id="a0bbd-157">Customize the HTTP REPL</span></span>

<span data-ttu-id="a0bbd-158">Les [couleurs](#set-color-preferences) de la boucle REPL HTTP peuvent être personnalisées.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-158">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="a0bbd-159">Vous pouvez aussi définir un [éditeur de texte par défaut](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="a0bbd-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="a0bbd-160">Les préférences de la boucle REPL HTTP sont conservées dans la session active et dans les sessions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-160">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="a0bbd-161">Une fois modifiées, les préférences sont stockées dans le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="a0bbd-162">Linux</span><span class="sxs-lookup"><span data-stu-id="a0bbd-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="a0bbd-163">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="a0bbd-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="a0bbd-164">MacOS</span><span class="sxs-lookup"><span data-stu-id="a0bbd-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="a0bbd-165">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="a0bbd-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="a0bbd-166">Windows</span><span class="sxs-lookup"><span data-stu-id="a0bbd-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="a0bbd-167">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="a0bbd-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="a0bbd-168">Le fichier *.httpreplprefs* est chargé au démarrage et ses modifications ne sont pas surveillées pendant l’exécution.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="a0bbd-169">Les modifications manuelles apportées au fichier prennent effet seulement après un redémarrage de l’outil.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="a0bbd-170">Voir les paramètres</span><span class="sxs-lookup"><span data-stu-id="a0bbd-170">View the settings</span></span>

<span data-ttu-id="a0bbd-171">Pour voir les paramètres disponibles, exécutez la commande `pref get`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="a0bbd-172">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="a0bbd-173">La commande précédente affiche les paires clé-valeur disponibles :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-173">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="a0bbd-174">Définir les préférences de couleur</span><span class="sxs-lookup"><span data-stu-id="a0bbd-174">Set color preferences</span></span>

<span data-ttu-id="a0bbd-175">La colorisation des réponses est actuellement prise en charge seulement pour JSON.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="a0bbd-176">Pour personnaliser les couleurs par défaut de l’outil REPL HTTP, localisez la clé correspondant à la couleur à changer.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-176">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="a0bbd-177">Pour des instructions sur la façon de trouver les clés, consultez la section [Voir les paramètres](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="a0bbd-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="a0bbd-178">Par exemple, remplacez la valeur de la clé `colors.json` de `Green` par `White` :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="a0bbd-179">Seules les [couleurs autorisées](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) peuvent être utilisées.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="a0bbd-180">Les requêtes HTTP suivantes affichent la sortie avec les nouvelles couleurs.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="a0bbd-181">Quand des clés d’une couleur spécifique ne sont pas définies, des clés plus génériques sont prises en compte.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="a0bbd-182">Pour illustrer ce comportement de repli, considérez l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="a0bbd-183">Si `colors.json.name` n’a pas de valeur, `colors.json.string` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="a0bbd-184">Si `colors.json.string` n’a pas de valeur, `colors.json.literal` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="a0bbd-185">Si `colors.json.literal` n’a pas de valeur, `colors.json` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="a0bbd-186">Si `colors.json` n’a pas de valeur, la couleur de texte par défaut de l’interpréteur de commandes (`AllowedColors.None`) est utilisée.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="a0bbd-187">Définir la taille de la mise en retrait</span><span class="sxs-lookup"><span data-stu-id="a0bbd-187">Set indentation size</span></span>

<span data-ttu-id="a0bbd-188">La personnalisation de la taille de la mise en retrait de la réponse est actuellement prise en charge pour JSON uniquement.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="a0bbd-189">La taille par défaut est de deux espaces.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-189">The default size is two spaces.</span></span> <span data-ttu-id="a0bbd-190">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-190">For example:</span></span>

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

<span data-ttu-id="a0bbd-191">Pour changer la taille par défaut, définissez la clé `formatting.json.indentSize`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="a0bbd-192">Par exemple, pour toujours utiliser quatre espaces :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="a0bbd-193">Les réponses suivantes adoptent la valeur correspondant à quatre espaces :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-193">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="a0bbd-194">Définir l’éditeur de texte par défaut</span><span class="sxs-lookup"><span data-stu-id="a0bbd-194">Set the default text editor</span></span>

<span data-ttu-id="a0bbd-195">Par défaut, la boucle REPL HTTP n’a pas d’éditeur de texte configuré pour l’utilisation.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-195">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="a0bbd-196">Pour tester les méthodes d’API web nécessitant un corps de requête HTTP, vous devez définir un éditeur de texte par défaut.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="a0bbd-197">L’outil REPL HTTP lance l’éditeur de texte configuré dans le seul but de permettre la composition du corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-197">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="a0bbd-198">Exécutez la commande suivante pour définir votre éditeur de texte préféré comme éditeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="a0bbd-199">Dans la commande précédente, `<EXECUTABLE>` est le chemin complet du fichier exécutable de l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="a0bbd-200">Par exemple, exécutez la commande suivante pour définir Visual Studio Code comme éditeur de texte par défaut :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="a0bbd-201">Linux</span><span class="sxs-lookup"><span data-stu-id="a0bbd-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="a0bbd-202">macOS</span><span class="sxs-lookup"><span data-stu-id="a0bbd-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="a0bbd-203">Windows</span><span class="sxs-lookup"><span data-stu-id="a0bbd-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="a0bbd-204">Pour lancer l’éditeur de texte par défaut avec des arguments CLI spécifiques, définissez la clé `editor.command.default.arguments`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="a0bbd-205">Par exemple, supposons que Visual Studio Code est l’éditeur de texte par défaut et que vous voulez que la boucle REPL HTTP ouvre toujours Visual Studio Code dans une nouvelle session avec les extensions désactivées.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-205">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="a0bbd-206">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="a0bbd-207">Si votre éditeur par défaut est Visual Studio Code, vous souhaiterez généralement passer l' `-w` `--wait` argument ou pour forcer Visual Studio code à attendre que vous fermiez le fichier avant de retourner.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="a0bbd-208">Définir les chemins de recherche de la description OpenAPI</span><span class="sxs-lookup"><span data-stu-id="a0bbd-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="a0bbd-209">Par défaut, HTTP REPL possède un ensemble de chemins d’accès relatifs qu’il utilise pour rechercher la description OpenAPI lors de l’exécution de la `connect` commande sans l' `--openapi` option.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-209">By default, the HTTP REPL has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="a0bbd-210">Ces chemins relatifs sont combinés avec les chemins racine et de base spécifiés dans la commande `connect`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="a0bbd-211">Les chemins relatifs par défaut sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-211">The default relative paths are:</span></span>

- <span data-ttu-id="a0bbd-212">*swagger.js*</span><span class="sxs-lookup"><span data-stu-id="a0bbd-212">*swagger.json*</span></span>
- <span data-ttu-id="a0bbd-213">*Swagger/v1/swagger.jssur*</span><span class="sxs-lookup"><span data-stu-id="a0bbd-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="a0bbd-214">*/swagger.jssur*</span><span class="sxs-lookup"><span data-stu-id="a0bbd-214">*/swagger.json*</span></span>
- <span data-ttu-id="a0bbd-215">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="a0bbd-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="a0bbd-216">*openapi.js*</span><span class="sxs-lookup"><span data-stu-id="a0bbd-216">*openapi.json*</span></span>
- <span data-ttu-id="a0bbd-217">*/openapi.jssur*</span><span class="sxs-lookup"><span data-stu-id="a0bbd-217">*/openapi.json*</span></span>

<span data-ttu-id="a0bbd-218">Pour utiliser un autre ensemble de chemins de recherche dans votre environnement, définissez la préférence `swagger.searchPaths`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="a0bbd-219">La valeur doit être une liste de chemins relatifs délimités par des barres verticales.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="a0bbd-220">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="a0bbd-221">Au lieu de remplacer entièrement la liste par défaut, la liste peut également être modifiée en ajoutant ou en supprimant des chemins.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="a0bbd-222">Pour ajouter un ou plusieurs chemins de recherche à la liste par défaut, définissez la `swagger.addToSearchPaths` préférence.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="a0bbd-223">La valeur doit être une liste de chemins relatifs délimités par des barres verticales.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="a0bbd-224">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="a0bbd-225">Pour supprimer un ou plusieurs chemins de recherche de la liste par défaut, définissez la `swagger.addToSearchPaths` préférence.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="a0bbd-226">La valeur doit être une liste de chemins relatifs délimités par des barres verticales.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="a0bbd-227">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="a0bbd-228">Tester des requêtes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="a0bbd-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="a0bbd-229">Synopsis</span><span class="sxs-lookup"><span data-stu-id="a0bbd-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="a0bbd-230">Arguments</span><span class="sxs-lookup"><span data-stu-id="a0bbd-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="a0bbd-231">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="a0bbd-232">Options</span><span class="sxs-lookup"><span data-stu-id="a0bbd-232">Options</span></span>

<span data-ttu-id="a0bbd-233">Les options suivantes sont disponibles pour la commande `get` :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="a0bbd-234">Exemple</span><span class="sxs-lookup"><span data-stu-id="a0bbd-234">Example</span></span>

<span data-ttu-id="a0bbd-235">Pour émettre une requête HTTP GET :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="a0bbd-236">Exécutez la commande `get` sur un point de terminaison qui la prend en charge :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="a0bbd-237">La commande précédente affiche le format de sortie suivant :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-237">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="a0bbd-238">Récupérez un enregistrement spécifique en passant un paramètre à la commande `get` :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="a0bbd-239">La commande précédente affiche le format de sortie suivant :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-239">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="a0bbd-240">Tester des requêtes HTTP POST</span><span class="sxs-lookup"><span data-stu-id="a0bbd-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="a0bbd-241">Synopsis</span><span class="sxs-lookup"><span data-stu-id="a0bbd-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="a0bbd-242">Arguments</span><span class="sxs-lookup"><span data-stu-id="a0bbd-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="a0bbd-243">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="a0bbd-244">Options</span><span class="sxs-lookup"><span data-stu-id="a0bbd-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="a0bbd-245">Exemple</span><span class="sxs-lookup"><span data-stu-id="a0bbd-245">Example</span></span>

<span data-ttu-id="a0bbd-246">Pour émettre une requête HTTP POST :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="a0bbd-247">Exécutez la commande `post` sur un point de terminaison qui la prend en charge :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="a0bbd-248">Dans la commande précédente, l’en-tête `Content-Type` de la requête HTTP est défini pour indiquer un type de média de corps de requête JSON.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="a0bbd-249">L’éditeur de texte par défaut ouvre un fichier *.tmp* avec un modèle JSON représentant le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="a0bbd-250">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="a0bbd-251">Pour définir l’éditeur de texte par défaut, consultez la section [Définir l’éditeur de texte par défaut](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="a0bbd-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="a0bbd-252">Modifiez le modèle JSON pour répondre aux exigences de validation du modèle :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="a0bbd-253">Enregistrez le fichier *.tmp* et fermez l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="a0bbd-254">La sortie suivante s’affiche dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-254">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="a0bbd-255">Tester des requêtes HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="a0bbd-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="a0bbd-256">Synopsis</span><span class="sxs-lookup"><span data-stu-id="a0bbd-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="a0bbd-257">Arguments</span><span class="sxs-lookup"><span data-stu-id="a0bbd-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="a0bbd-258">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="a0bbd-259">Options</span><span class="sxs-lookup"><span data-stu-id="a0bbd-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="a0bbd-260">Exemple</span><span class="sxs-lookup"><span data-stu-id="a0bbd-260">Example</span></span>

<span data-ttu-id="a0bbd-261">Pour émettre une requête HTTP PUT :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="a0bbd-262">*Facultatif* : exécutez la `get` commande pour afficher les données avant de les modifier :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-262">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="a0bbd-263">Exécutez la commande `put` sur un point de terminaison qui la prend en charge :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="a0bbd-264">Dans la commande précédente, l’en-tête `Content-Type` de la requête HTTP est défini pour indiquer un type de média de corps de requête JSON.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="a0bbd-265">L’éditeur de texte par défaut ouvre un fichier *.tmp* avec un modèle JSON représentant le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="a0bbd-266">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="a0bbd-267">Pour définir l’éditeur de texte par défaut, consultez la section [Définir l’éditeur de texte par défaut](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="a0bbd-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="a0bbd-268">Modifiez le modèle JSON pour répondre aux exigences de validation du modèle :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="a0bbd-269">Enregistrez le fichier *.tmp* et fermez l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="a0bbd-270">La sortie suivante s’affiche dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="a0bbd-271">*Facultatif* : émettez une `get` commande pour voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-271">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="a0bbd-272">Par exemple, si vous avez tapé « cerise » dans l’éditeur de texte, un `get` retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="a0bbd-273">Tester des requêtes HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="a0bbd-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="a0bbd-274">Synopsis</span><span class="sxs-lookup"><span data-stu-id="a0bbd-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="a0bbd-275">Arguments</span><span class="sxs-lookup"><span data-stu-id="a0bbd-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="a0bbd-276">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="a0bbd-277">Options</span><span class="sxs-lookup"><span data-stu-id="a0bbd-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="a0bbd-278">Exemple</span><span class="sxs-lookup"><span data-stu-id="a0bbd-278">Example</span></span>

<span data-ttu-id="a0bbd-279">Pour émettre une requête HTTP DELETE :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="a0bbd-280">*Facultatif* : exécutez la `get` commande pour afficher les données avant de les modifier :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-280">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="a0bbd-281">Exécutez la commande `delete` sur un point de terminaison qui la prend en charge :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="a0bbd-282">La commande précédente affiche le format de sortie suivant :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="a0bbd-283">*Facultatif* : émettez une `get` commande pour voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-283">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="a0bbd-284">Dans cet exemple, un `get` retourne la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-284">In this example, a `get` returns the following output:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="a0bbd-285">Tester des requêtes HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="a0bbd-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="a0bbd-286">Synopsis</span><span class="sxs-lookup"><span data-stu-id="a0bbd-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="a0bbd-287">Arguments</span><span class="sxs-lookup"><span data-stu-id="a0bbd-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="a0bbd-288">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="a0bbd-289">Options</span><span class="sxs-lookup"><span data-stu-id="a0bbd-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="a0bbd-290">Tester des requêtes HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="a0bbd-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="a0bbd-291">Synopsis</span><span class="sxs-lookup"><span data-stu-id="a0bbd-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="a0bbd-292">Arguments</span><span class="sxs-lookup"><span data-stu-id="a0bbd-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="a0bbd-293">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="a0bbd-294">Options</span><span class="sxs-lookup"><span data-stu-id="a0bbd-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="a0bbd-295">Tester des requêtes HTTP OPTIONS</span><span class="sxs-lookup"><span data-stu-id="a0bbd-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="a0bbd-296">Synopsis</span><span class="sxs-lookup"><span data-stu-id="a0bbd-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="a0bbd-297">Arguments</span><span class="sxs-lookup"><span data-stu-id="a0bbd-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="a0bbd-298">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="a0bbd-299">Options</span><span class="sxs-lookup"><span data-stu-id="a0bbd-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="a0bbd-300">Définir des en-têtes de requête HTTP</span><span class="sxs-lookup"><span data-stu-id="a0bbd-300">Set HTTP request headers</span></span>

<span data-ttu-id="a0bbd-301">Pour définir un en-tête de requête HTTP, utilisez une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="a0bbd-302">Définir inline avec la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="a0bbd-303">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="a0bbd-304">Avec l’approche précédente, chaque en-tête de requête HTTP distinct nécessite sa propre option `-h`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="a0bbd-305">Définir avant l’envoi de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="a0bbd-306">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="a0bbd-307">Si l’en-tête est défini avant l’envoi d’une requête, l’en-tête reste défini pour la durée de la session de l’interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="a0bbd-308">Pour effacer l’en-tête, spécifiez une valeur vide.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="a0bbd-309">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="a0bbd-310">Tester les points de terminaison sécurisés</span><span class="sxs-lookup"><span data-stu-id="a0bbd-310">Test secured endpoints</span></span>

<span data-ttu-id="a0bbd-311">La réplication HTTP prend en charge le test des points de terminaison sécurisés des manières suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-311">The HTTP REPL supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="a0bbd-312">Par le biais des informations d’identification par défaut de l’utilisateur connecté.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="a0bbd-313">À l’aide des en-têtes de requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="a0bbd-314">Informations d’identification par défaut</span><span class="sxs-lookup"><span data-stu-id="a0bbd-314">Default credentials</span></span>

<span data-ttu-id="a0bbd-315">Prenons l’exemple d’une API Web que vous testez et qui est hébergée dans IIS et sécurisée avec l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="a0bbd-316">Vous souhaitez que les informations d’identification de l’utilisateur qui exécute l’outil soient transmises aux points de terminaison HTTP en cours de test.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="a0bbd-317">Pour transmettre les informations d’identification par défaut de l’utilisateur connecté :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="a0bbd-318">Définissez la `httpClient.useDefaultCredentials` préférence sur `true` :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="a0bbd-319">Quittez et redémarrez l’outil avant d’envoyer une autre demande à l’API Web.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="a0bbd-320">Informations d’identification du proxy par défaut</span><span class="sxs-lookup"><span data-stu-id="a0bbd-320">Default proxy credentials</span></span>

<span data-ttu-id="a0bbd-321">Imaginez un scénario dans lequel l’API Web que vous testez se trouve derrière un proxy sécurisé avec l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="a0bbd-322">Vous souhaitez que les informations d’identification de l’utilisateur qui exécute l’outil soient acheminées vers le proxy.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="a0bbd-323">Pour transmettre les informations d’identification par défaut de l’utilisateur connecté :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="a0bbd-324">Définissez la `httpClient.proxy.useDefaultCredentials` préférence sur `true` :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="a0bbd-325">Quittez et redémarrez l’outil avant d’envoyer une autre demande à l’API Web.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="a0bbd-326">En-têtes de requête HTTP</span><span class="sxs-lookup"><span data-stu-id="a0bbd-326">HTTP request headers</span></span>

<span data-ttu-id="a0bbd-327">Voici quelques exemples de modèles d’authentification et d’autorisation pris en charge :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="a0bbd-328">basic authentication</span><span class="sxs-lookup"><span data-stu-id="a0bbd-328">basic authentication</span></span>
* <span data-ttu-id="a0bbd-329">Jetons du porteur JWT</span><span class="sxs-lookup"><span data-stu-id="a0bbd-329">JWT bearer tokens</span></span>
* <span data-ttu-id="a0bbd-330">authentification Digest</span><span class="sxs-lookup"><span data-stu-id="a0bbd-330">digest authentication</span></span>

<span data-ttu-id="a0bbd-331">Par exemple, vous pouvez envoyer un jeton de porteur à un point de terminaison à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="a0bbd-332">Pour accéder à un point de terminaison hébergé par Azure ou pour utiliser l' [API REST Azure](/rest/api/azure/), vous avez besoin d’un jeton de porteur.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="a0bbd-333">Procédez comme suit pour obtenir un jeton de porteur pour votre abonnement Azure via le [Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="a0bbd-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="a0bbd-334">Le REPL HTTP définit le jeton du porteur dans un en-tête de requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-334">The HTTP REPL sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="a0bbd-335">Une liste de Azure App Service Web Apps est récupérée.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="a0bbd-336">Connectez-vous à Azure :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="a0bbd-337">Récupérez votre ID d’abonnement à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="a0bbd-338">Copiez votre ID d’abonnement et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="a0bbd-339">Récupérez votre jeton de porteur avec la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="a0bbd-340">Connectez-vous à l’API REST Azure via la réplication HTTP :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-340">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="a0bbd-341">Définissez l' `Authorization` en-tête de requête http :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="a0bbd-342">Accédez à l’abonnement :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="a0bbd-343">Obtenir la liste des Web Apps Azure App Service de votre abonnement :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="a0bbd-344">La réponse suivante s’affiche :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-344">The following response is displayed:</span></span>

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

## <a name="toggle-http-request-display"></a><span data-ttu-id="a0bbd-345">Activer/désactiver l’affichage des requêtes HTTP</span><span class="sxs-lookup"><span data-stu-id="a0bbd-345">Toggle HTTP request display</span></span>

<span data-ttu-id="a0bbd-346">Par défaut, l’affichage de la requête HTTP envoyée est supprimé.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="a0bbd-347">Il est possible de changer le paramètre correspondant pour la durée de la session de l’interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="a0bbd-348">Activer l’affichage des requêtes</span><span class="sxs-lookup"><span data-stu-id="a0bbd-348">Enable request display</span></span>

<span data-ttu-id="a0bbd-349">Affichez la requête HTTP envoyée en exécutant la commande `echo on`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="a0bbd-350">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="a0bbd-351">Les requêtes HTTP suivantes dans la session active affichent les en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="a0bbd-352">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-352">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="a0bbd-353">Désactiver l’affichage des requêtes</span><span class="sxs-lookup"><span data-stu-id="a0bbd-353">Disable request display</span></span>

<span data-ttu-id="a0bbd-354">Supprimez l’affichage de la requête HTTP envoyée en exécutant la commande `echo off`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="a0bbd-355">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="a0bbd-356">Exécuter un script</span><span class="sxs-lookup"><span data-stu-id="a0bbd-356">Run a script</span></span>

<span data-ttu-id="a0bbd-357">Si vous exécutez fréquemment le même jeu de commandes REPL HTTP, envisagez de les stocker dans un fichier texte.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-357">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="a0bbd-358">Les commandes du fichier prennent la même forme que les commandes exécutées manuellement sur la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="a0bbd-359">Les commandes peuvent être exécutées de façon groupée avec la commande `run`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="a0bbd-360">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-360">For example:</span></span>

1. <span data-ttu-id="a0bbd-361">Créez un fichier texte contenant un ensemble de commandes délimitées par des sauts de ligne.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="a0bbd-362">Pour illustrer ceci, considérez un fichier *people-script.txt* contenant les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="a0bbd-363">Exécutez la commande `run`, en passant le chemin du fichier texte.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="a0bbd-364">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="a0bbd-365">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-365">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="a0bbd-366">Effacer la sortie</span><span class="sxs-lookup"><span data-stu-id="a0bbd-366">Clear the output</span></span>

<span data-ttu-id="a0bbd-367">Pour supprimer toutes les sorties écrites dans l’interpréteur de commandes par l’outil REPL HTTP, exécutez la commande `clear` ou `cls`.</span><span class="sxs-lookup"><span data-stu-id="a0bbd-367">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="a0bbd-368">À titre d’illustration, imaginez que l’interpréteur de commandes contient la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-368">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="a0bbd-369">Exécutez la commande suivante pour effacer la sortie :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="a0bbd-370">Après l’exécution de la commande précédente, l’interpréteur de commandes contient seulement la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="a0bbd-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="a0bbd-371">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a0bbd-371">Additional resources</span></span>

* [<span data-ttu-id="a0bbd-372">Requêtes de l’API REST</span><span class="sxs-lookup"><span data-stu-id="a0bbd-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="a0bbd-373">Dépôt GitHub REPL HTTP</span><span class="sxs-lookup"><span data-stu-id="a0bbd-373">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
