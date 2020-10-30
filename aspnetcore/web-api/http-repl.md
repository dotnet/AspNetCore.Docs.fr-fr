---
title: Tester des API web avec la boucle REPL HTTP
author: scottaddie
description: Découvrez comment utiliser l’outil global REPL HTTP de .NET Core pour parcourir et tester une API web ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 05/20/2020
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
ms.openlocfilehash: efd2208044ad6392131216266afc34187d738b78
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058972"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="acaff-103">Tester des API web avec la boucle REPL HTTP</span><span class="sxs-lookup"><span data-stu-id="acaff-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="acaff-104">Par [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="acaff-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="acaff-105">La boucle REPL (Read-Eval-Print Loop) HTTP est :</span><span class="sxs-lookup"><span data-stu-id="acaff-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="acaff-106">Un outil en ligne de commande léger et multiplateforme qui est pris en charge partout où .NET Core est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="acaff-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="acaff-107">Utilisée pour effectuer des requêtes HTTP pour tester des API web ASP.NET Core (et des API web ASP.NET Core non-ASP.NET) et voir leurs résultats.</span><span class="sxs-lookup"><span data-stu-id="acaff-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="acaff-108">Capable de tester les API web hébergées dans n’importe quel environnement, notamment localhost et Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="acaff-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="acaff-109">Les [verbes HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) suivants sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="acaff-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="acaff-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="acaff-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="acaff-111">GET</span><span class="sxs-lookup"><span data-stu-id="acaff-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="acaff-112">SIÈGE</span><span class="sxs-lookup"><span data-stu-id="acaff-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="acaff-113">Options</span><span class="sxs-lookup"><span data-stu-id="acaff-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="acaff-114">CORRECTIF</span><span class="sxs-lookup"><span data-stu-id="acaff-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="acaff-115">POST</span><span class="sxs-lookup"><span data-stu-id="acaff-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="acaff-116">PUT</span><span class="sxs-lookup"><span data-stu-id="acaff-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="acaff-117">Pour continuer, [consultez ou téléchargez l’exemple d’API web ASP.NET Core](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([comment télécharger](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="acaff-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="acaff-118">Prérequis</span><span class="sxs-lookup"><span data-stu-id="acaff-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="acaff-119">Installation</span><span class="sxs-lookup"><span data-stu-id="acaff-119">Installation</span></span>

<span data-ttu-id="acaff-120">Pour installer la boucle REPL HTTP, exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="acaff-121">Un [outil global .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) est installé à partir du package NuGet [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl).</span><span class="sxs-lookup"><span data-stu-id="acaff-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="acaff-122">Utilisation</span><span class="sxs-lookup"><span data-stu-id="acaff-122">Usage</span></span>

<span data-ttu-id="acaff-123">Une fois l’installation de l’outil réussie, exécutez la commande suivante pour démarrer la boucle REPL HTTP :</span><span class="sxs-lookup"><span data-stu-id="acaff-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="acaff-124">Pour voir les commandes de la boucle REPL HTTP disponibles, exécutez une des commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="acaff-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="acaff-125">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-125">The following output is displayed:</span></span>

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

set base       Set the base URI. e.g. `set base http://locahost:5000`
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

<span data-ttu-id="acaff-126">La boucle REPL HTTP offre la complétion des commandes.</span><span class="sxs-lookup"><span data-stu-id="acaff-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="acaff-127">Un appui sur la touche <kbd>Tab</kbd> itère au sein de la liste des commandes qui complètent les caractères ou le point de terminaison d’API que vous avez tapés.</span><span class="sxs-lookup"><span data-stu-id="acaff-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="acaff-128">Les sections suivantes décrivent les commandes CLI disponibles.</span><span class="sxs-lookup"><span data-stu-id="acaff-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="acaff-129">Se connecter à l’API web</span><span class="sxs-lookup"><span data-stu-id="acaff-129">Connect to the web API</span></span>

<span data-ttu-id="acaff-130">Connectez-vous à une API web en exécutant la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="acaff-131">`<ROOT URI>` est l’URI de base pour l’API web.</span><span class="sxs-lookup"><span data-stu-id="acaff-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="acaff-132">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="acaff-133">Vous pouvez également exécuter la commande suivante à tout moment pendant l’exécution de la boucle REPL HTTP :</span><span class="sxs-lookup"><span data-stu-id="acaff-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="acaff-134">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="acaff-135">Pointer manuellement sur le document Swagger pour l’API web</span><span class="sxs-lookup"><span data-stu-id="acaff-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="acaff-136">La commande connect ci-dessus tente de trouver automatiquement le document Swagger.</span><span class="sxs-lookup"><span data-stu-id="acaff-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="acaff-137">Si, pour une raison quelconque, elle n’y parvient pas, vous pouvez spécifier l’URI du document Swagger pour l’API web à l’aide de l’option `--swagger` :</span><span class="sxs-lookup"><span data-stu-id="acaff-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="acaff-138">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="acaff-139">Accéder à l’API web</span><span class="sxs-lookup"><span data-stu-id="acaff-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="acaff-140">Voir les points de terminaison disponibles</span><span class="sxs-lookup"><span data-stu-id="acaff-140">View available endpoints</span></span>

<span data-ttu-id="acaff-141">Pour lister les différents points de terminaison (contrôleurs) sur le chemin actuel de l’adresse de l’API web, exécutez la commande `ls` ou `dir` :</span><span class="sxs-lookup"><span data-stu-id="acaff-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="acaff-142">Le format de sortie suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="acaff-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="acaff-143">La sortie précédente indique que deux contrôleurs sont disponibles : `Fruits` et `People`.</span><span class="sxs-lookup"><span data-stu-id="acaff-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="acaff-144">Les deux contrôleurs prennent en charge les opérations HTTP GET et POST sans paramètres.</span><span class="sxs-lookup"><span data-stu-id="acaff-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="acaff-145">La navigation dans un contrôleur spécifique révèle plus de détails.</span><span class="sxs-lookup"><span data-stu-id="acaff-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="acaff-146">Par exemple, la sortie de la commande suivante indique que le contrôleur `Fruits` prend également en charge les opérations HTTP GET, PUT et DELETE.</span><span class="sxs-lookup"><span data-stu-id="acaff-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="acaff-147">Chacune de ces opérations attend un paramètre `id` dans la route :</span><span class="sxs-lookup"><span data-stu-id="acaff-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="acaff-148">Vous pouvez également exécuter la commande `ui` pour ouvrir la page de l’interface utilisateur Swagger de l’API web dans un navigateur.</span><span class="sxs-lookup"><span data-stu-id="acaff-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="acaff-149">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="acaff-150">Accéder à un point de terminaison</span><span class="sxs-lookup"><span data-stu-id="acaff-150">Navigate to an endpoint</span></span>

<span data-ttu-id="acaff-151">Pour accéder à un autre point de terminaison sur l’API web, exécutez la commande `cd` :</span><span class="sxs-lookup"><span data-stu-id="acaff-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="acaff-152">Le chemin qui suit la commande `cd` ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="acaff-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="acaff-153">Le format de sortie suivant s’affiche :</span><span class="sxs-lookup"><span data-stu-id="acaff-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="acaff-154">Personnaliser la boucle REPL HTTP</span><span class="sxs-lookup"><span data-stu-id="acaff-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="acaff-155">Les [couleurs](#set-color-preferences) de la boucle REPL HTTP peuvent être personnalisées.</span><span class="sxs-lookup"><span data-stu-id="acaff-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="acaff-156">Vous pouvez aussi définir un [éditeur de texte par défaut](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="acaff-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="acaff-157">Les préférences de la boucle REPL HTTP sont conservées dans la session active et dans les sessions ultérieures.</span><span class="sxs-lookup"><span data-stu-id="acaff-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="acaff-158">Une fois modifiées, les préférences sont stockées dans le fichier suivant :</span><span class="sxs-lookup"><span data-stu-id="acaff-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="acaff-159">Linux</span><span class="sxs-lookup"><span data-stu-id="acaff-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="acaff-160">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="acaff-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="acaff-161">macOS</span><span class="sxs-lookup"><span data-stu-id="acaff-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="acaff-162">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="acaff-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="acaff-163">Windows</span><span class="sxs-lookup"><span data-stu-id="acaff-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="acaff-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="acaff-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="acaff-165">Le fichier *.httpreplprefs* est chargé au démarrage et ses modifications ne sont pas surveillées pendant l’exécution.</span><span class="sxs-lookup"><span data-stu-id="acaff-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="acaff-166">Les modifications manuelles apportées au fichier prennent effet seulement après un redémarrage de l’outil.</span><span class="sxs-lookup"><span data-stu-id="acaff-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="acaff-167">Voir les paramètres</span><span class="sxs-lookup"><span data-stu-id="acaff-167">View the settings</span></span>

<span data-ttu-id="acaff-168">Pour voir les paramètres disponibles, exécutez la commande `pref get`.</span><span class="sxs-lookup"><span data-stu-id="acaff-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="acaff-169">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="acaff-170">La commande précédente affiche les paires clé-valeur disponibles :</span><span class="sxs-lookup"><span data-stu-id="acaff-170">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="acaff-171">Définir les préférences de couleur</span><span class="sxs-lookup"><span data-stu-id="acaff-171">Set color preferences</span></span>

<span data-ttu-id="acaff-172">La colorisation des réponses est actuellement prise en charge seulement pour JSON.</span><span class="sxs-lookup"><span data-stu-id="acaff-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="acaff-173">Pour personnaliser les couleurs par défaut de l’outil REPL HTTP, localisez la clé correspondant à la couleur à changer.</span><span class="sxs-lookup"><span data-stu-id="acaff-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="acaff-174">Pour des instructions sur la façon de trouver les clés, consultez la section [Voir les paramètres](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="acaff-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="acaff-175">Par exemple, remplacez la valeur de la clé `colors.json` de `Green` par `White` :</span><span class="sxs-lookup"><span data-stu-id="acaff-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="acaff-176">Seules les [couleurs autorisées](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) peuvent être utilisées.</span><span class="sxs-lookup"><span data-stu-id="acaff-176">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="acaff-177">Les requêtes HTTP suivantes affichent la sortie avec les nouvelles couleurs.</span><span class="sxs-lookup"><span data-stu-id="acaff-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="acaff-178">Quand des clés d’une couleur spécifique ne sont pas définies, des clés plus génériques sont prises en compte.</span><span class="sxs-lookup"><span data-stu-id="acaff-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="acaff-179">Pour illustrer ce comportement de repli, considérez l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="acaff-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="acaff-180">Si `colors.json.name` n’a pas de valeur, `colors.json.string` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="acaff-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="acaff-181">Si `colors.json.string` n’a pas de valeur, `colors.json.literal` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="acaff-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="acaff-182">Si `colors.json.literal` n’a pas de valeur, `colors.json` est utilisée.</span><span class="sxs-lookup"><span data-stu-id="acaff-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="acaff-183">Si `colors.json` n’a pas de valeur, la couleur de texte par défaut de l’interpréteur de commandes (`AllowedColors.None`) est utilisée.</span><span class="sxs-lookup"><span data-stu-id="acaff-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="acaff-184">Définir la taille de la mise en retrait</span><span class="sxs-lookup"><span data-stu-id="acaff-184">Set indentation size</span></span>

<span data-ttu-id="acaff-185">La personnalisation de la taille de la mise en retrait de la réponse est actuellement prise en charge pour JSON uniquement.</span><span class="sxs-lookup"><span data-stu-id="acaff-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="acaff-186">La taille par défaut est de deux espaces.</span><span class="sxs-lookup"><span data-stu-id="acaff-186">The default size is two spaces.</span></span> <span data-ttu-id="acaff-187">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-187">For example:</span></span>

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

<span data-ttu-id="acaff-188">Pour changer la taille par défaut, définissez la clé `formatting.json.indentSize`.</span><span class="sxs-lookup"><span data-stu-id="acaff-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="acaff-189">Par exemple, pour toujours utiliser quatre espaces :</span><span class="sxs-lookup"><span data-stu-id="acaff-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="acaff-190">Les réponses suivantes adoptent la valeur correspondant à quatre espaces :</span><span class="sxs-lookup"><span data-stu-id="acaff-190">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="acaff-191">Définir l’éditeur de texte par défaut</span><span class="sxs-lookup"><span data-stu-id="acaff-191">Set the default text editor</span></span>

<span data-ttu-id="acaff-192">Par défaut, la boucle REPL HTTP n’a pas d’éditeur de texte configuré pour l’utilisation.</span><span class="sxs-lookup"><span data-stu-id="acaff-192">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="acaff-193">Pour tester les méthodes d’API web nécessitant un corps de requête HTTP, vous devez définir un éditeur de texte par défaut.</span><span class="sxs-lookup"><span data-stu-id="acaff-193">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="acaff-194">L’outil REPL HTTP lance l’éditeur de texte configuré dans le seul but de permettre la composition du corps de la requête.</span><span class="sxs-lookup"><span data-stu-id="acaff-194">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="acaff-195">Exécutez la commande suivante pour définir votre éditeur de texte préféré comme éditeur par défaut :</span><span class="sxs-lookup"><span data-stu-id="acaff-195">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="acaff-196">Dans la commande précédente, `<EXECUTABLE>` est le chemin complet du fichier exécutable de l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="acaff-196">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="acaff-197">Par exemple, exécutez la commande suivante pour définir Visual Studio Code comme éditeur de texte par défaut :</span><span class="sxs-lookup"><span data-stu-id="acaff-197">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="acaff-198">Linux</span><span class="sxs-lookup"><span data-stu-id="acaff-198">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="acaff-199">macOS</span><span class="sxs-lookup"><span data-stu-id="acaff-199">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="acaff-200">Windows</span><span class="sxs-lookup"><span data-stu-id="acaff-200">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="acaff-201">Pour lancer l’éditeur de texte par défaut avec des arguments CLI spécifiques, définissez la clé `editor.command.default.arguments`.</span><span class="sxs-lookup"><span data-stu-id="acaff-201">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="acaff-202">Par exemple, supposons que Visual Studio Code est l’éditeur de texte par défaut et que vous voulez que la boucle REPL HTTP ouvre toujours Visual Studio Code dans une nouvelle session avec les extensions désactivées.</span><span class="sxs-lookup"><span data-stu-id="acaff-202">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="acaff-203">Exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-203">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="acaff-204">Définir les chemins de recherche Swagger</span><span class="sxs-lookup"><span data-stu-id="acaff-204">Set the Swagger search paths</span></span>

<span data-ttu-id="acaff-205">Par défaut, HTTP REPL possède un ensemble de chemins relatifs qu’il utilise pour rechercher le document Swagger lors de l'exécution de la commande `connect` sans l’option `--swagger`.</span><span class="sxs-lookup"><span data-stu-id="acaff-205">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="acaff-206">Ces chemins relatifs sont combinés avec les chemins racine et de base spécifiés dans la commande `connect`.</span><span class="sxs-lookup"><span data-stu-id="acaff-206">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="acaff-207">Les chemins relatifs par défaut sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="acaff-207">The default relative paths are:</span></span>

- <span data-ttu-id="acaff-208">*swagger.js*</span><span class="sxs-lookup"><span data-stu-id="acaff-208">*swagger.json*</span></span>
- <span data-ttu-id="acaff-209">*Swagger/v1/swagger.jssur*</span><span class="sxs-lookup"><span data-stu-id="acaff-209">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="acaff-210">*/swagger.jssur*</span><span class="sxs-lookup"><span data-stu-id="acaff-210">*/swagger.json*</span></span>
- <span data-ttu-id="acaff-211">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="acaff-211">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="acaff-212">Pour utiliser un autre ensemble de chemins de recherche dans votre environnement, définissez la préférence `swagger.searchPaths`.</span><span class="sxs-lookup"><span data-stu-id="acaff-212">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="acaff-213">La valeur doit être une liste de chemins relatifs délimités par des barres verticales.</span><span class="sxs-lookup"><span data-stu-id="acaff-213">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="acaff-214">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-214">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="acaff-215">Tester des requêtes HTTP GET</span><span class="sxs-lookup"><span data-stu-id="acaff-215">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="acaff-216">Synopsis</span><span class="sxs-lookup"><span data-stu-id="acaff-216">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="acaff-217">Arguments</span><span class="sxs-lookup"><span data-stu-id="acaff-217">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="acaff-218">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="acaff-218">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="acaff-219">Options</span><span class="sxs-lookup"><span data-stu-id="acaff-219">Options</span></span>

<span data-ttu-id="acaff-220">Les options suivantes sont disponibles pour la commande `get` :</span><span class="sxs-lookup"><span data-stu-id="acaff-220">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="acaff-221">Exemple</span><span class="sxs-lookup"><span data-stu-id="acaff-221">Example</span></span>

<span data-ttu-id="acaff-222">Pour émettre une requête HTTP GET :</span><span class="sxs-lookup"><span data-stu-id="acaff-222">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="acaff-223">Exécutez la commande `get` sur un point de terminaison qui la prend en charge :</span><span class="sxs-lookup"><span data-stu-id="acaff-223">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="acaff-224">La commande précédente affiche le format de sortie suivant :</span><span class="sxs-lookup"><span data-stu-id="acaff-224">The preceding command displays the following output format:</span></span>

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


    https://localhost:5001/people~
    ```

1. <span data-ttu-id="acaff-225">Récupérez un enregistrement spécifique en passant un paramètre à la commande `get` :</span><span class="sxs-lookup"><span data-stu-id="acaff-225">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="acaff-226">La commande précédente affiche le format de sortie suivant :</span><span class="sxs-lookup"><span data-stu-id="acaff-226">The preceding command displays the following output format:</span></span>

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


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="acaff-227">Tester des requêtes HTTP POST</span><span class="sxs-lookup"><span data-stu-id="acaff-227">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="acaff-228">Synopsis</span><span class="sxs-lookup"><span data-stu-id="acaff-228">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="acaff-229">Arguments</span><span class="sxs-lookup"><span data-stu-id="acaff-229">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="acaff-230">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="acaff-230">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="acaff-231">Options</span><span class="sxs-lookup"><span data-stu-id="acaff-231">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="acaff-232">Exemple</span><span class="sxs-lookup"><span data-stu-id="acaff-232">Example</span></span>

<span data-ttu-id="acaff-233">Pour émettre une requête HTTP POST :</span><span class="sxs-lookup"><span data-stu-id="acaff-233">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="acaff-234">Exécutez la commande `post` sur un point de terminaison qui la prend en charge :</span><span class="sxs-lookup"><span data-stu-id="acaff-234">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="acaff-235">Dans la commande précédente, l’en-tête `Content-Type` de la requête HTTP est défini pour indiquer un type de média de corps de requête JSON.</span><span class="sxs-lookup"><span data-stu-id="acaff-235">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="acaff-236">L’éditeur de texte par défaut ouvre un fichier *.tmp* avec un modèle JSON représentant le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="acaff-236">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="acaff-237">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-237">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="acaff-238">Pour définir l’éditeur de texte par défaut, consultez la section [Définir l’éditeur de texte par défaut](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="acaff-238">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="acaff-239">Modifiez le modèle JSON pour répondre aux exigences de validation du modèle :</span><span class="sxs-lookup"><span data-stu-id="acaff-239">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="acaff-240">Enregistrez le fichier *.tmp* et fermez l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="acaff-240">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="acaff-241">La sortie suivante s’affiche dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="acaff-241">The following output appears in the command shell:</span></span>

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


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="acaff-242">Tester des requêtes HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="acaff-242">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="acaff-243">Synopsis</span><span class="sxs-lookup"><span data-stu-id="acaff-243">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="acaff-244">Arguments</span><span class="sxs-lookup"><span data-stu-id="acaff-244">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="acaff-245">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="acaff-245">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="acaff-246">Options</span><span class="sxs-lookup"><span data-stu-id="acaff-246">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="acaff-247">Exemple</span><span class="sxs-lookup"><span data-stu-id="acaff-247">Example</span></span>

<span data-ttu-id="acaff-248">Pour émettre une requête HTTP PUT :</span><span class="sxs-lookup"><span data-stu-id="acaff-248">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="acaff-249">*Facultatif* : exécutez la `get` commande pour afficher les données avant de les modifier :</span><span class="sxs-lookup"><span data-stu-id="acaff-249">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
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

1. <span data-ttu-id="acaff-250">Exécutez la commande `put` sur un point de terminaison qui la prend en charge :</span><span class="sxs-lookup"><span data-stu-id="acaff-250">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="acaff-251">Dans la commande précédente, l’en-tête `Content-Type` de la requête HTTP est défini pour indiquer un type de média de corps de requête JSON.</span><span class="sxs-lookup"><span data-stu-id="acaff-251">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="acaff-252">L’éditeur de texte par défaut ouvre un fichier *.tmp* avec un modèle JSON représentant le corps de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="acaff-252">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="acaff-253">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-253">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="acaff-254">Pour définir l’éditeur de texte par défaut, consultez la section [Définir l’éditeur de texte par défaut](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="acaff-254">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="acaff-255">Modifiez le modèle JSON pour répondre aux exigences de validation du modèle :</span><span class="sxs-lookup"><span data-stu-id="acaff-255">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="acaff-256">Enregistrez le fichier *.tmp* et fermez l’éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="acaff-256">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="acaff-257">La sortie suivante s’affiche dans l’interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="acaff-257">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="acaff-258">*Facultatif* : émettez une `get` commande pour voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="acaff-258">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="acaff-259">Par exemple, si vous avez tapé « Cherry » dans l’éditeur de texte, une commande `get` retourne ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="acaff-259">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
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


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="acaff-260">Tester des requêtes HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="acaff-260">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="acaff-261">Synopsis</span><span class="sxs-lookup"><span data-stu-id="acaff-261">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="acaff-262">Arguments</span><span class="sxs-lookup"><span data-stu-id="acaff-262">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="acaff-263">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="acaff-263">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="acaff-264">Options</span><span class="sxs-lookup"><span data-stu-id="acaff-264">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="acaff-265">Exemple</span><span class="sxs-lookup"><span data-stu-id="acaff-265">Example</span></span>

<span data-ttu-id="acaff-266">Pour émettre une requête HTTP DELETE :</span><span class="sxs-lookup"><span data-stu-id="acaff-266">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="acaff-267">*Facultatif* : exécutez la `get` commande pour afficher les données avant de les modifier :</span><span class="sxs-lookup"><span data-stu-id="acaff-267">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
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

1. <span data-ttu-id="acaff-268">Exécutez la commande `delete` sur un point de terminaison qui la prend en charge :</span><span class="sxs-lookup"><span data-stu-id="acaff-268">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="acaff-269">La commande précédente affiche le format de sortie suivant :</span><span class="sxs-lookup"><span data-stu-id="acaff-269">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="acaff-270">*Facultatif* : émettez une `get` commande pour voir les modifications.</span><span class="sxs-lookup"><span data-stu-id="acaff-270">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="acaff-271">Dans cet exemple, une commande `get` retourne ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="acaff-271">In this example, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
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


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="acaff-272">Tester des requêtes HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="acaff-272">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="acaff-273">Synopsis</span><span class="sxs-lookup"><span data-stu-id="acaff-273">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="acaff-274">Arguments</span><span class="sxs-lookup"><span data-stu-id="acaff-274">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="acaff-275">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="acaff-275">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="acaff-276">Options</span><span class="sxs-lookup"><span data-stu-id="acaff-276">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="acaff-277">Tester des requêtes HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="acaff-277">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="acaff-278">Synopsis</span><span class="sxs-lookup"><span data-stu-id="acaff-278">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="acaff-279">Arguments</span><span class="sxs-lookup"><span data-stu-id="acaff-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="acaff-280">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="acaff-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="acaff-281">Options</span><span class="sxs-lookup"><span data-stu-id="acaff-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="acaff-282">Tester des requêtes HTTP OPTIONS</span><span class="sxs-lookup"><span data-stu-id="acaff-282">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="acaff-283">Synopsis</span><span class="sxs-lookup"><span data-stu-id="acaff-283">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="acaff-284">Arguments</span><span class="sxs-lookup"><span data-stu-id="acaff-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="acaff-285">Paramètre de route, le cas échéant, attendu par la méthode d’action du contrôleur associé.</span><span class="sxs-lookup"><span data-stu-id="acaff-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="acaff-286">Options</span><span class="sxs-lookup"><span data-stu-id="acaff-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="acaff-287">Définir des en-têtes de requête HTTP</span><span class="sxs-lookup"><span data-stu-id="acaff-287">Set HTTP request headers</span></span>

<span data-ttu-id="acaff-288">Pour définir un en-tête de requête HTTP, utilisez une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="acaff-288">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="acaff-289">Définir inline avec la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="acaff-289">Set inline with the HTTP request.</span></span> <span data-ttu-id="acaff-290">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-290">For example:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="acaff-291">Avec l’approche précédente, chaque en-tête de requête HTTP distinct nécessite sa propre option `-h`.</span><span class="sxs-lookup"><span data-stu-id="acaff-291">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="acaff-292">Définir avant l’envoi de la requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="acaff-292">Set before sending the HTTP request.</span></span> <span data-ttu-id="acaff-293">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-293">For example:</span></span>

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    <span data-ttu-id="acaff-294">Si l’en-tête est défini avant l’envoi d’une requête, l’en-tête reste défini pour la durée de la session de l’interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="acaff-294">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="acaff-295">Pour effacer l’en-tête, spécifiez une valeur vide.</span><span class="sxs-lookup"><span data-stu-id="acaff-295">To clear the header, provide an empty value.</span></span> <span data-ttu-id="acaff-296">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-296">For example:</span></span>
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="acaff-297">Tester les points de terminaison sécurisés</span><span class="sxs-lookup"><span data-stu-id="acaff-297">Test secured endpoints</span></span>

<span data-ttu-id="acaff-298">La réplication HTTP prend en charge le test des points de terminaison sécurisés de deux façons : via les informations d’identification par défaut de l’utilisateur connecté ou via l’utilisation d’en-têtes de requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="acaff-298">The HTTP REPL supports the testing of secured endpoints in two ways: via the default credentials of the logged in user or through the use of HTTP request headers.</span></span> 

### <a name="default-credentials"></a><span data-ttu-id="acaff-299">Informations d’identification par défaut</span><span class="sxs-lookup"><span data-stu-id="acaff-299">Default credentials</span></span>

<span data-ttu-id="acaff-300">Imaginez un scénario dans lequel l’API Web que vous testez est hébergée dans IIS et est sécurisée avec l’authentification Windows.</span><span class="sxs-lookup"><span data-stu-id="acaff-300">Consider a scenario in which the web API you're testing is hosted in IIS and is secured with Windows authentication.</span></span> <span data-ttu-id="acaff-301">Vous souhaitez que les informations d’identification de l’utilisateur qui exécute l’outil soient transmises aux points de terminaison HTTP en cours de test.</span><span class="sxs-lookup"><span data-stu-id="acaff-301">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="acaff-302">Pour transmettre les informations d’identification par défaut de l’utilisateur connecté :</span><span class="sxs-lookup"><span data-stu-id="acaff-302">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="acaff-303">Définissez la `httpClient.useDefaultCredentials` préférence sur `true` :</span><span class="sxs-lookup"><span data-stu-id="acaff-303">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="acaff-304">Quittez et redémarrez l’outil avant d’envoyer une autre demande à l’API Web.</span><span class="sxs-lookup"><span data-stu-id="acaff-304">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="acaff-305">En-têtes de requête HTTP</span><span class="sxs-lookup"><span data-stu-id="acaff-305">HTTP request headers</span></span>

<span data-ttu-id="acaff-306">L’authentification de base, les jetons de porteur JWT et l’authentification Digest sont des exemples de modèles d’authentification et d’autorisation pris en charge.</span><span class="sxs-lookup"><span data-stu-id="acaff-306">Examples of supported authentication and authorization schemes include basic authentication, JWT bearer tokens, and digest authentication.</span></span> <span data-ttu-id="acaff-307">Par exemple, vous pouvez envoyer un jeton de porteur à un point de terminaison à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-307">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="acaff-308">Pour accéder à un point de terminaison hébergé par Azure ou pour utiliser l' [API REST Azure](/rest/api/azure/), vous avez besoin d’un jeton de porteur.</span><span class="sxs-lookup"><span data-stu-id="acaff-308">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="acaff-309">Procédez comme suit pour obtenir un jeton de porteur pour votre abonnement Azure via le [Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="acaff-309">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="acaff-310">Le REPL HTTP définit le jeton du porteur dans un en-tête de requête HTTP et récupère une liste de Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="acaff-310">The HTTP REPL sets the bearer token in an HTTP request header and retrieves a list of Azure App Service Web Apps.</span></span>

1. <span data-ttu-id="acaff-311">Connexion à Azure :</span><span class="sxs-lookup"><span data-stu-id="acaff-311">Log in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="acaff-312">Récupérez votre ID d’abonnement à l’aide de la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-312">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="acaff-313">Copiez votre ID d’abonnement et exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-313">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="acaff-314">Récupérez votre jeton de porteur avec la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-314">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="acaff-315">Connectez-vous à l’API REST Azure via la réplication HTTP :</span><span class="sxs-lookup"><span data-stu-id="acaff-315">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="acaff-316">Définissez l' `Authorization` en-tête de requête http :</span><span class="sxs-lookup"><span data-stu-id="acaff-316">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="acaff-317">Accédez à l’abonnement :</span><span class="sxs-lookup"><span data-stu-id="acaff-317">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="acaff-318">Obtenir la liste des Web Apps Azure App Service de votre abonnement :</span><span class="sxs-lookup"><span data-stu-id="acaff-318">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="acaff-319">La réponse suivante s’affiche :</span><span class="sxs-lookup"><span data-stu-id="acaff-319">The following response is displayed:</span></span>

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

## <a name="toggle-http-request-display"></a><span data-ttu-id="acaff-320">Activer/désactiver l’affichage des requêtes HTTP</span><span class="sxs-lookup"><span data-stu-id="acaff-320">Toggle HTTP request display</span></span>

<span data-ttu-id="acaff-321">Par défaut, l’affichage de la requête HTTP envoyée est supprimé.</span><span class="sxs-lookup"><span data-stu-id="acaff-321">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="acaff-322">Il est possible de changer le paramètre correspondant pour la durée de la session de l’interpréteur de commandes.</span><span class="sxs-lookup"><span data-stu-id="acaff-322">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="acaff-323">Activer l’affichage des requêtes</span><span class="sxs-lookup"><span data-stu-id="acaff-323">Enable request display</span></span>

<span data-ttu-id="acaff-324">Affichez la requête HTTP envoyée en exécutant la commande `echo on`.</span><span class="sxs-lookup"><span data-stu-id="acaff-324">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="acaff-325">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-325">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="acaff-326">Les requêtes HTTP suivantes dans la session active affichent les en-têtes de requête.</span><span class="sxs-lookup"><span data-stu-id="acaff-326">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="acaff-327">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-327">For example:</span></span>

```console
https://localhost:5001/people~ post

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


https://localhost:5001/people~
```

### <a name="disable-request-display"></a><span data-ttu-id="acaff-328">Désactiver l’affichage des requêtes</span><span class="sxs-lookup"><span data-stu-id="acaff-328">Disable request display</span></span>

<span data-ttu-id="acaff-329">Supprimez l’affichage de la requête HTTP envoyée en exécutant la commande `echo off`.</span><span class="sxs-lookup"><span data-stu-id="acaff-329">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="acaff-330">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-330">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="acaff-331">Exécuter un script</span><span class="sxs-lookup"><span data-stu-id="acaff-331">Run a script</span></span>

<span data-ttu-id="acaff-332">Si vous exécutez fréquemment le même jeu de commandes REPL HTTP, envisagez de les stocker dans un fichier texte.</span><span class="sxs-lookup"><span data-stu-id="acaff-332">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="acaff-333">Les commandes placées dans le fichier sont de la même forme que celles exécutées manuellement sur la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="acaff-333">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="acaff-334">Les commandes peuvent être exécutées de façon groupée avec la commande `run`.</span><span class="sxs-lookup"><span data-stu-id="acaff-334">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="acaff-335">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-335">For example:</span></span>

1. <span data-ttu-id="acaff-336">Créez un fichier texte contenant un ensemble de commandes délimitées par des sauts de ligne.</span><span class="sxs-lookup"><span data-stu-id="acaff-336">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="acaff-337">Pour illustrer ceci, considérez un fichier *people-script.txt* contenant les commandes suivantes :</span><span class="sxs-lookup"><span data-stu-id="acaff-337">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="acaff-338">Exécutez la commande `run`, en passant le chemin du fichier texte.</span><span class="sxs-lookup"><span data-stu-id="acaff-338">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="acaff-339">Exemple :</span><span class="sxs-lookup"><span data-stu-id="acaff-339">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="acaff-340">Vous obtenez la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-340">The following output appears:</span></span>

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a><span data-ttu-id="acaff-341">Effacer la sortie</span><span class="sxs-lookup"><span data-stu-id="acaff-341">Clear the output</span></span>

<span data-ttu-id="acaff-342">Pour supprimer toutes les sorties écrites dans l’interpréteur de commandes par l’outil REPL HTTP, exécutez la commande `clear` ou `cls`.</span><span class="sxs-lookup"><span data-stu-id="acaff-342">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="acaff-343">À titre d’illustration, imaginez que l’interpréteur de commandes contient la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-343">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="acaff-344">Exécutez la commande suivante pour effacer la sortie :</span><span class="sxs-lookup"><span data-stu-id="acaff-344">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="acaff-345">Après l’exécution de la commande précédente, l’interpréteur de commandes contient seulement la sortie suivante :</span><span class="sxs-lookup"><span data-stu-id="acaff-345">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="acaff-346">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="acaff-346">Additional resources</span></span>

* [<span data-ttu-id="acaff-347">Requêtes de l’API REST</span><span class="sxs-lookup"><span data-stu-id="acaff-347">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="acaff-348">Dépôt GitHub REPL HTTP</span><span class="sxs-lookup"><span data-stu-id="acaff-348">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
