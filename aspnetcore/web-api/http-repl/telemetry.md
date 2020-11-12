---
title: Télémétrie HttpRepl
author: scottaddie
description: En savoir plus sur les données de télémétrie collectées par le HttpRepl.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550606"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="05ab0-103">Télémétrie HttpRepl</span><span class="sxs-lookup"><span data-stu-id="05ab0-103">HttpRepl telemetry</span></span>

<span data-ttu-id="05ab0-104">[HttpRepl](xref:web-api/http-repl) comprend une fonctionnalité de télémétrie qui collecte les données d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="05ab0-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="05ab0-105">Il est important que l’équipe HttpRepl comprenne comment l’outil est utilisé pour qu’il puisse être amélioré.</span><span class="sxs-lookup"><span data-stu-id="05ab0-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="05ab0-106">Comment désactiver la fonctionnalité</span><span class="sxs-lookup"><span data-stu-id="05ab0-106">How to opt out</span></span>

<span data-ttu-id="05ab0-107">La fonctionnalité de télémétrie HttpRepl est activée par défaut.</span><span class="sxs-lookup"><span data-stu-id="05ab0-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="05ab0-108">Pour désactiver la fonctionnalité de télémétrie, définissez la variable d’environnement `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` sur `1` ou `true`.</span><span class="sxs-lookup"><span data-stu-id="05ab0-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="05ab0-109">Divulgation d’informations</span><span class="sxs-lookup"><span data-stu-id="05ab0-109">Disclosure</span></span>

<span data-ttu-id="05ab0-110">Le HttpRepl affiche un texte similaire à ce qui suit lorsque vous exécutez pour la première fois l’outil.</span><span class="sxs-lookup"><span data-stu-id="05ab0-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="05ab0-111">Le texte peut varier légèrement en fonction de la version de l’outil que vous exécutez.</span><span class="sxs-lookup"><span data-stu-id="05ab0-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="05ab0-112">Lors de cette première exécution, Microsoft vous informe sur la collecte de données.</span><span class="sxs-lookup"><span data-stu-id="05ab0-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="05ab0-113">Pour supprimer le texte d’expérience « première exécution », définissez la `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` variable d’environnement sur `1` ou `true` .</span><span class="sxs-lookup"><span data-stu-id="05ab0-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="05ab0-114">Points de données</span><span class="sxs-lookup"><span data-stu-id="05ab0-114">Data points</span></span>

<span data-ttu-id="05ab0-115">La fonctionnalité de télémétrie n’est pas :</span><span class="sxs-lookup"><span data-stu-id="05ab0-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="05ab0-116">Recueillez des données personnelles, telles que des noms d’utilisateur, des adresses de messagerie ou des URL.</span><span class="sxs-lookup"><span data-stu-id="05ab0-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="05ab0-117">Analyser vos requêtes ou réponses HTTP.</span><span class="sxs-lookup"><span data-stu-id="05ab0-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="05ab0-118">Les données sont envoyées en toute sécurité aux serveurs Microsoft et conservées sous accès restreint.</span><span class="sxs-lookup"><span data-stu-id="05ab0-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="05ab0-119">Nous prenons la protection de vos données au sérieux.</span><span class="sxs-lookup"><span data-stu-id="05ab0-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="05ab0-120">Si vous pensez que la fonctionnalité de télémétrie collecte des données sensibles ou que les données sont gérées de manière inappropriée ou non, effectuez l’une des actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="05ab0-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="05ab0-121">Déposez un problème dans le référentiel [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) .</span><span class="sxs-lookup"><span data-stu-id="05ab0-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="05ab0-122">Envoyer un e-mail à [dotnet@microsoft.com](mailto:dotnet@microsoft.com) pour investigation.</span><span class="sxs-lookup"><span data-stu-id="05ab0-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="05ab0-123">La fonctionnalité de télémétrie collecte les données suivantes.</span><span class="sxs-lookup"><span data-stu-id="05ab0-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="05ab0-124">Versions du SDK .NET</span><span class="sxs-lookup"><span data-stu-id="05ab0-124">.NET SDK versions</span></span> | <span data-ttu-id="05ab0-125">Données</span><span class="sxs-lookup"><span data-stu-id="05ab0-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="05ab0-126">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-126">>=5.0</span></span>        | <span data-ttu-id="05ab0-127">Horodatage de l’appel.</span><span class="sxs-lookup"><span data-stu-id="05ab0-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="05ab0-128">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-128">>=5.0</span></span>        | <span data-ttu-id="05ab0-129">Adresse IP de trois octets utilisée pour déterminer l’emplacement géographique.</span><span class="sxs-lookup"><span data-stu-id="05ab0-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="05ab0-130">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-130">>=5.0</span></span>        | <span data-ttu-id="05ab0-131">Système d’exploitation et version.</span><span class="sxs-lookup"><span data-stu-id="05ab0-131">Operating system and version.</span></span> |
| <span data-ttu-id="05ab0-132">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-132">>=5.0</span></span>        | <span data-ttu-id="05ab0-133">ID d’exécution (RID) sur lequel l’outil est exécuté.</span><span class="sxs-lookup"><span data-stu-id="05ab0-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="05ab0-134">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-134">>=5.0</span></span>        | <span data-ttu-id="05ab0-135">Indique si l’outil s’exécute dans un conteneur.</span><span class="sxs-lookup"><span data-stu-id="05ab0-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="05ab0-136">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-136">>=5.0</span></span>        | <span data-ttu-id="05ab0-137">Adresse du Access Control de média haché (MAC) : un ID (SHA256) haché et unique pour un ordinateur.</span><span class="sxs-lookup"><span data-stu-id="05ab0-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="05ab0-138">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-138">>=5.0</span></span>        | <span data-ttu-id="05ab0-139">Version du noyau.</span><span class="sxs-lookup"><span data-stu-id="05ab0-139">Kernel version.</span></span> |
| <span data-ttu-id="05ab0-140">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-140">>=5.0</span></span>        | <span data-ttu-id="05ab0-141">Version de HttpRepl.</span><span class="sxs-lookup"><span data-stu-id="05ab0-141">HttpRepl version.</span></span> |
| <span data-ttu-id="05ab0-142">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-142">>=5.0</span></span>        | <span data-ttu-id="05ab0-143">Si l’outil a été démarré avec les `help` `run` arguments, ou `connect` .</span><span class="sxs-lookup"><span data-stu-id="05ab0-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="05ab0-144">Les valeurs d’argument réelles ne sont pas collectées.</span><span class="sxs-lookup"><span data-stu-id="05ab0-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="05ab0-145">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-145">>=5.0</span></span>        | <span data-ttu-id="05ab0-146">Commande appelée (par exemple, `get` ) et si elle a réussi.</span><span class="sxs-lookup"><span data-stu-id="05ab0-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="05ab0-147">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-147">>=5.0</span></span>        | <span data-ttu-id="05ab0-148">Pour la `connect` commande, si les `root` `base` arguments, ou `openapi` ont été fournis.</span><span class="sxs-lookup"><span data-stu-id="05ab0-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="05ab0-149">Les valeurs d’argument réelles ne sont pas collectées.</span><span class="sxs-lookup"><span data-stu-id="05ab0-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="05ab0-150">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-150">>=5.0</span></span>        | <span data-ttu-id="05ab0-151">Pour la `pref` commande, si un `get` ou `set` a été émis et quelles préférences ont été consultées.</span><span class="sxs-lookup"><span data-stu-id="05ab0-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="05ab0-152">S’il ne s’agit pas d’une préférence connue, le nom est haché.</span><span class="sxs-lookup"><span data-stu-id="05ab0-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="05ab0-153">La valeur n’est pas collectée.</span><span class="sxs-lookup"><span data-stu-id="05ab0-153">The value isn't collected.</span></span> |
| <span data-ttu-id="05ab0-154">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-154">>=5.0</span></span>        | <span data-ttu-id="05ab0-155">Pour la `set header` commande, le nom d’en-tête est défini.</span><span class="sxs-lookup"><span data-stu-id="05ab0-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="05ab0-156">Si ce n’est pas un en-tête bien connu, le nom est haché.</span><span class="sxs-lookup"><span data-stu-id="05ab0-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="05ab0-157">La valeur n’est pas collectée.</span><span class="sxs-lookup"><span data-stu-id="05ab0-157">The value isn't collected.</span></span> |
| <span data-ttu-id="05ab0-158">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-158">>=5.0</span></span>        | <span data-ttu-id="05ab0-159">Pour la `connect` commande, si un cas spécial pour `dotnet new webapi` a été utilisé et, s’il a été contourné par la préférence.</span><span class="sxs-lookup"><span data-stu-id="05ab0-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="05ab0-160">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="05ab0-160">>=5.0</span></span>        | <span data-ttu-id="05ab0-161">Pour toutes les commandes HTTP (par exemple, obtenir, poster, PUT), si chacune des options a été spécifiée.</span><span class="sxs-lookup"><span data-stu-id="05ab0-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="05ab0-162">Les valeurs des options ne sont pas collectées.</span><span class="sxs-lookup"><span data-stu-id="05ab0-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="05ab0-163">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="05ab0-163">Additional resources</span></span>

* [<span data-ttu-id="05ab0-164">Télémétrie du kit SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="05ab0-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="05ab0-165">CLI .NET Core des données de télémétrie</span><span class="sxs-lookup"><span data-stu-id="05ab0-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
