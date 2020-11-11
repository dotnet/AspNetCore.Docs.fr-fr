---
title: Télémétrie HTTP REPL
author: scottaddie
description: En savoir plus sur les données de télémétrie collectées par la REPL HTTP.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502032"
---
# <a name="http-repl-telemetry"></a><span data-ttu-id="50235-103">Télémétrie HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="50235-103">HTTP REPL telemetry</span></span>

<span data-ttu-id="50235-104">La [boucle de lecture-eval-Print (REPL) http](xref:web-api/http-repl) comprend une fonctionnalité de télémétrie qui collecte les données d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="50235-104">The [HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="50235-105">Il est important que l’équipe de réplication HTTP comprenne comment l’outil est utilisé pour qu’il puisse être amélioré.</span><span class="sxs-lookup"><span data-stu-id="50235-105">It's important that the HTTP REPL team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="50235-106">Comment désactiver la fonctionnalité</span><span class="sxs-lookup"><span data-stu-id="50235-106">How to opt out</span></span>

<span data-ttu-id="50235-107">La fonctionnalité de télémétrie HTTP REPL est activée par défaut.</span><span class="sxs-lookup"><span data-stu-id="50235-107">The HTTP REPL telemetry feature is enabled by default.</span></span> <span data-ttu-id="50235-108">Pour désactiver la fonctionnalité de télémétrie, définissez la variable d’environnement `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` sur `1` ou `true`.</span><span class="sxs-lookup"><span data-stu-id="50235-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="50235-109">Divulgation d’informations</span><span class="sxs-lookup"><span data-stu-id="50235-109">Disclosure</span></span>

<span data-ttu-id="50235-110">HttpRepl affiche un texte similaire à ce qui suit lorsque vous exécutez l’outil pour la première fois.</span><span class="sxs-lookup"><span data-stu-id="50235-110">HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="50235-111">Le texte peut varier légèrement en fonction de la version de l’outil que vous exécutez.</span><span class="sxs-lookup"><span data-stu-id="50235-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="50235-112">Lors de cette première exécution, Microsoft vous informe sur la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="50235-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a><span data-ttu-id="50235-113">Points de données</span><span class="sxs-lookup"><span data-stu-id="50235-113">Data points</span></span>

<span data-ttu-id="50235-114">La fonctionnalité de télémétrie n’est pas :</span><span class="sxs-lookup"><span data-stu-id="50235-114">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="50235-115">Recueillez des données personnelles, telles que des noms d’utilisateur, des adresses de messagerie ou des URL.</span><span class="sxs-lookup"><span data-stu-id="50235-115">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="50235-116">Analyser vos requêtes ou réponses HTTP.</span><span class="sxs-lookup"><span data-stu-id="50235-116">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="50235-117">Les données sont envoyées en toute sécurité aux serveurs Microsoft et conservées sous accès restreint.</span><span class="sxs-lookup"><span data-stu-id="50235-117">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="50235-118">Nous prenons la protection de vos données au sérieux.</span><span class="sxs-lookup"><span data-stu-id="50235-118">Protecting your privacy is important to us.</span></span> <span data-ttu-id="50235-119">Si vous pensez que la fonctionnalité de télémétrie collecte des données sensibles ou que les données sont gérées de manière inappropriée ou non, effectuez l’une des actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="50235-119">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="50235-120">Déposez un problème dans le référentiel [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="50235-120">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="50235-121">Envoyer un e-mail à [dotnet@microsoft.com](mailto:dotnet@microsoft.com) pour investigation.</span><span class="sxs-lookup"><span data-stu-id="50235-121">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="50235-122">La fonctionnalité de télémétrie collecte les données suivantes.</span><span class="sxs-lookup"><span data-stu-id="50235-122">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="50235-123">Versions du SDK .NET</span><span class="sxs-lookup"><span data-stu-id="50235-123">.NET SDK versions</span></span> | <span data-ttu-id="50235-124">Données</span><span class="sxs-lookup"><span data-stu-id="50235-124">Data</span></span> |
|--------------|------|
| <span data-ttu-id="50235-125">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-125">>=5.0</span></span>        | <span data-ttu-id="50235-126">Horodatage de l’appel.</span><span class="sxs-lookup"><span data-stu-id="50235-126">Timestamp of invocation.</span></span> |
| <span data-ttu-id="50235-127">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-127">>=5.0</span></span>        | <span data-ttu-id="50235-128">Adresse IP de trois octets utilisée pour déterminer l’emplacement géographique.</span><span class="sxs-lookup"><span data-stu-id="50235-128">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="50235-129">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-129">>=5.0</span></span>        | <span data-ttu-id="50235-130">Système d’exploitation et version.</span><span class="sxs-lookup"><span data-stu-id="50235-130">Operating system and version.</span></span> |
| <span data-ttu-id="50235-131">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-131">>=5.0</span></span>        | <span data-ttu-id="50235-132">ID d’exécution (RID) sur lequel l’outil est exécuté.</span><span class="sxs-lookup"><span data-stu-id="50235-132">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="50235-133">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-133">>=5.0</span></span>        | <span data-ttu-id="50235-134">Indique si l’outil s’exécute dans un conteneur.</span><span class="sxs-lookup"><span data-stu-id="50235-134">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="50235-135">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-135">>=5.0</span></span>        | <span data-ttu-id="50235-136">Adresse du Access Control de média haché (MAC) : un ID (SHA256) haché et unique pour un ordinateur.</span><span class="sxs-lookup"><span data-stu-id="50235-136">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="50235-137">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-137">>=5.0</span></span>        | <span data-ttu-id="50235-138">Version du noyau.</span><span class="sxs-lookup"><span data-stu-id="50235-138">Kernel version.</span></span> |
| <span data-ttu-id="50235-139">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-139">>=5.0</span></span>        | <span data-ttu-id="50235-140">Version de REPL HTTP.</span><span class="sxs-lookup"><span data-stu-id="50235-140">HTTP REPL version.</span></span> |
| <span data-ttu-id="50235-141">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-141">>=5.0</span></span>        | <span data-ttu-id="50235-142">Si l’outil a été démarré avec les `help` `run` arguments, ou `connect` .</span><span class="sxs-lookup"><span data-stu-id="50235-142">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="50235-143">Les valeurs d’argument réelles ne sont pas collectées.</span><span class="sxs-lookup"><span data-stu-id="50235-143">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="50235-144">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-144">>=5.0</span></span>        | <span data-ttu-id="50235-145">Commande appelée (par exemple, `get` ) et si elle a réussi.</span><span class="sxs-lookup"><span data-stu-id="50235-145">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="50235-146">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-146">>=5.0</span></span>        | <span data-ttu-id="50235-147">Pour la `connect` commande, si les `root` `base` arguments, ou `openapi` ont été fournis.</span><span class="sxs-lookup"><span data-stu-id="50235-147">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="50235-148">Les valeurs d’argument réelles ne sont pas collectées.</span><span class="sxs-lookup"><span data-stu-id="50235-148">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="50235-149">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-149">>=5.0</span></span>        | <span data-ttu-id="50235-150">Pour la `pref` commande, si un `get` ou `set` a été émis et quelles préférences ont été consultées.</span><span class="sxs-lookup"><span data-stu-id="50235-150">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="50235-151">S’il ne s’agit pas d’une préférence connue, le nom est haché.</span><span class="sxs-lookup"><span data-stu-id="50235-151">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="50235-152">La valeur n’est pas collectée.</span><span class="sxs-lookup"><span data-stu-id="50235-152">The value isn't collected.</span></span> |
| <span data-ttu-id="50235-153">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-153">>=5.0</span></span>        | <span data-ttu-id="50235-154">Pour la `set header` commande, le nom d’en-tête est défini.</span><span class="sxs-lookup"><span data-stu-id="50235-154">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="50235-155">Si ce n’est pas un en-tête bien connu, le nom est haché.</span><span class="sxs-lookup"><span data-stu-id="50235-155">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="50235-156">La valeur n’est pas collectée.</span><span class="sxs-lookup"><span data-stu-id="50235-156">The value isn't collected.</span></span> |
| <span data-ttu-id="50235-157">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-157">>=5.0</span></span>        | <span data-ttu-id="50235-158">Pour la `connect` commande, si un cas spécial pour `dotnet new webapi` a été utilisé et, s’il a été contourné par la préférence.</span><span class="sxs-lookup"><span data-stu-id="50235-158">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="50235-159">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="50235-159">>=5.0</span></span>        | <span data-ttu-id="50235-160">Pour toutes les commandes HTTP (par exemple, obtenir, poster, PUT), si chacune des options a été spécifiée.</span><span class="sxs-lookup"><span data-stu-id="50235-160">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="50235-161">Les valeurs des options ne sont pas collectées.</span><span class="sxs-lookup"><span data-stu-id="50235-161">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="50235-162">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="50235-162">Additional resources</span></span>

* [<span data-ttu-id="50235-163">Télémétrie du kit SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="50235-163">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="50235-164">CLI .NET Core des données de télémétrie</span><span class="sxs-lookup"><span data-stu-id="50235-164">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
