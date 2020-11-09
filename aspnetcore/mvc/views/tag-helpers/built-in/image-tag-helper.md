---
title: Tag Helper Image dans ASP.NET Core
author: pkellner
description: Montre comment utiliser un Tag Helper Image.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: edc6971ac42756504b5b371ba509d7a657a0c396
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061117"
---
# <a name="image-tag-helper-in-aspnet-core"></a><span data-ttu-id="46559-103">Tag Helper Image dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46559-103">Image Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="46559-104">Par [Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="46559-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="46559-105">Le Tag Helper Image améliore la balise `<img>` afin de fournir un comportement de cache busting pour les fichiers image statiques.</span><span class="sxs-lookup"><span data-stu-id="46559-105">The Image Tag Helper enhances the `<img>` tag to provide cache-busting behavior for static image files.</span></span>

<span data-ttu-id="46559-106">Une chaîne de cache busting est une valeur unique représentant le hachage du fichier image statique ajouté à l’URL de la ressource.</span><span class="sxs-lookup"><span data-stu-id="46559-106">A cache-busting string is a unique value representing the hash of the static image file appended to the asset's URL.</span></span> <span data-ttu-id="46559-107">La chaîne unique invite les clients (et certains proxys) à recharger l’image depuis le serveur web hôte et non depuis le cache du client.</span><span class="sxs-lookup"><span data-stu-id="46559-107">The unique string prompts clients (and some proxies) to reload the image from the host web server and not from the client's cache.</span></span>

<span data-ttu-id="46559-108">Si la source de l’image (`src`) est un fichier statique sur le serveur web hôte :</span><span class="sxs-lookup"><span data-stu-id="46559-108">If the image source (`src`) is a static file on the host web server:</span></span>

* <span data-ttu-id="46559-109">Une chaîne de cache-busting unique est ajoutée en tant que paramètre de requête à la source de l’image.</span><span class="sxs-lookup"><span data-stu-id="46559-109">A unique cache-busting string is appended as a query parameter to the image source.</span></span>
* <span data-ttu-id="46559-110">Si le fichier sur le serveur web hôte change, une URL de demande unique qui inclut le paramètre de demande mis à jour est générée.</span><span class="sxs-lookup"><span data-stu-id="46559-110">If the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span>

<span data-ttu-id="46559-111">Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="46559-111">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="46559-112">Attributs de Tag Helper Image</span><span class="sxs-lookup"><span data-stu-id="46559-112">Image Tag Helper Attributes</span></span>

### <a name="src"></a><span data-ttu-id="46559-113">src</span><span class="sxs-lookup"><span data-stu-id="46559-113">src</span></span>

<span data-ttu-id="46559-114">Pour activer le Tag Helper Image, l’attribut `src` est obligatoire sur l’élément `<img>`.</span><span class="sxs-lookup"><span data-stu-id="46559-114">To activate the Image Tag Helper, the `src` attribute is required on the `<img>` element.</span></span>

<span data-ttu-id="46559-115">La source de l’image (`src`) doit pointer vers un fichier statique physique sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="46559-115">The image source (`src`) must point to a physical static file on the server.</span></span> <span data-ttu-id="46559-116">Si la source `src` est un URI distant, le paramètre de la chaîne de requête de cache-busting n’est pas généré.</span><span class="sxs-lookup"><span data-stu-id="46559-116">If the `src` is a remote URI, the cache-busting query string parameter isn't generated.</span></span>

### <a name="asp-append-version"></a><span data-ttu-id="46559-117">asp-append-version</span><span class="sxs-lookup"><span data-stu-id="46559-117">asp-append-version</span></span>

<span data-ttu-id="46559-118">Quand `asp-append-version` est spécifié avec une valeur `true` et un attribut `src`, le Tag Helper Image est appelé.</span><span class="sxs-lookup"><span data-stu-id="46559-118">When `asp-append-version` is specified with a `true` value along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="46559-119">L’exemple suivant utilise un Tag Helper Image :</span><span class="sxs-lookup"><span data-stu-id="46559-119">The following example uses an Image Tag Helper:</span></span>

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

<span data-ttu-id="46559-120">Si le fichier statique existe dans le répertoire */wwwroot/images/* , le code HTML généré est semblable au suivant (le hachage sera différent) :</span><span class="sxs-lookup"><span data-stu-id="46559-120">If the static file exists in the directory */wwwroot/images/* , the generated HTML is similar to the following (the hash will be different):</span></span>

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

<span data-ttu-id="46559-121">La valeur affectée au paramètre `v` est la valeur de hachage du fichier *asplogo.png* sur le disque.</span><span class="sxs-lookup"><span data-stu-id="46559-121">The value assigned to the parameter `v` is the hash value of the *asplogo.png* file on disk.</span></span> <span data-ttu-id="46559-122">Si le serveur web ne peut pas obtenir l’accès en lecture au fichier statique, aucun paramètre `v` n’est ajouté à l’attribut `src` dans le balisage affiché.</span><span class="sxs-lookup"><span data-stu-id="46559-122">If the web server is unable to obtain read access to the static file, no `v` parameter is added to the `src` attribute in the rendered markup.</span></span>

## <a name="hash-caching-behavior"></a><span data-ttu-id="46559-123">Comportement de mise en cache du hachage</span><span class="sxs-lookup"><span data-stu-id="46559-123">Hash caching behavior</span></span>

<span data-ttu-id="46559-124">Le Tag Helper Image utilise le fournisseur de cache sur le serveur web local pour stocker le hachage `Sha512` calculé d’un fichier donné.</span><span class="sxs-lookup"><span data-stu-id="46559-124">The Image Tag Helper uses the cache provider on the local web server to store the calculated `Sha512` hash of a given file.</span></span> <span data-ttu-id="46559-125">Si le fichier est demandé plusieurs fois, le hachage n’est pas recalculé.</span><span class="sxs-lookup"><span data-stu-id="46559-125">If the file is requested multiple times, the hash isn't recalculated.</span></span> <span data-ttu-id="46559-126">Le cache est invalidé par un observateur de fichier qui est associé au fichier quand le hachage `Sha512` du fichier est calculé.</span><span class="sxs-lookup"><span data-stu-id="46559-126">The cache is invalidated by a file watcher that's attached to the file when the file's `Sha512` hash is calculated.</span></span> <span data-ttu-id="46559-127">Quand le fichier change sur le disque, un nouveau hachage est calculé et mis en cache.</span><span class="sxs-lookup"><span data-stu-id="46559-127">When the file changes on disk, a new hash is calculated and cached.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="46559-128">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="46559-128">Additional resources</span></span>

* <xref:performance/caching/memory>
