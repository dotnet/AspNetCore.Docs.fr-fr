---
title: Tag Helper de script dans ASP.NET Core
author: rick-anderson
ms.author: riande
description: Découvrez les attributs d’assistance de balise de script ASP.NET Core et le rôle joué par chaque attribut lors de l’extension du comportement de la balise de script HTML.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- 'appsettings.json'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053499"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="03cf6-103">Tag Helper de script dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03cf6-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="03cf6-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="03cf6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="03cf6-105">Le [tag Helper script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) génère un lien vers un fichier de script principal ou de retour.</span><span class="sxs-lookup"><span data-stu-id="03cf6-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="03cf6-106">En général, le fichier de script principal se trouve sur un [réseau de distribution de contenu](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="03cf6-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="03cf6-107">Le tag Helper script vous permet de spécifier un CDN pour le fichier de script et un secours lorsque le CDN n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="03cf6-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="03cf6-108">Le tag Helper script fournit l’avantage en termes de performances d’un CDN avec la robustesse de l’hébergement local.</span><span class="sxs-lookup"><span data-stu-id="03cf6-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="03cf6-109">Le Razor balisage suivant montre un `script` élément avec une solution de secours :</span><span class="sxs-lookup"><span data-stu-id="03cf6-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="03cf6-110">N’utilisez pas l' `<script>` attribut [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) de l’élément pour différer le chargement du script CDN.</span><span class="sxs-lookup"><span data-stu-id="03cf6-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="03cf6-111">Le tag Helper script rend JavaScript qui exécute immédiatement l’expression [asp-Fallback-test](#asp-fallback-test) .</span><span class="sxs-lookup"><span data-stu-id="03cf6-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="03cf6-112">L’expression échoue si le chargement du script CDN est différé.</span><span class="sxs-lookup"><span data-stu-id="03cf6-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="03cf6-113">Attributs d’assistance de balise de script couramment utilisés</span><span class="sxs-lookup"><span data-stu-id="03cf6-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="03cf6-114">Consultez [l’aide de balises de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) pour obtenir tous les attributs, propriétés et méthodes du programme d’assistance des balises de script.</span><span class="sxs-lookup"><span data-stu-id="03cf6-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="03cf6-115">ASP-Fallback-test</span><span class="sxs-lookup"><span data-stu-id="03cf6-115">asp-fallback-test</span></span>

<span data-ttu-id="03cf6-116">Méthode de script définie dans le script principal à utiliser pour le test de secours.</span><span class="sxs-lookup"><span data-stu-id="03cf6-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="03cf6-117">Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="03cf6-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="03cf6-118">ASP-Fallback-SRC</span><span class="sxs-lookup"><span data-stu-id="03cf6-118">asp-fallback-src</span></span>

<span data-ttu-id="03cf6-119">URL d’une balise de script vers laquelle effectuer un secours dans le cas où le principal échoue.</span><span class="sxs-lookup"><span data-stu-id="03cf6-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="03cf6-120">Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="03cf6-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03cf6-121">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="03cf6-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
