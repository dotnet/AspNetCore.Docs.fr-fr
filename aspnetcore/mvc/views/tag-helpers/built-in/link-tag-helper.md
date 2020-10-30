---
title: Lier tag Helper dans ASP.NET Core
author: rick-anderson
ms.author: riande
description: Découvrez les attributs d’assistance de balise de lien ASP.NET Core et le rôle joué par chaque attribut lors de l’extension du comportement de la balise de lien HTML.
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 7998cec94ebb56cbe6dbc321f7cb499260d6fe74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059115"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="d7c8b-103">Lier tag Helper dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7c8b-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="d7c8b-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d7c8b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d7c8b-105">Le [tag Helper Link](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) génère un lien vers un fichier CSS principal ou de retour.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="d7c8b-106">En général, le fichier CSS principal se trouve sur un [réseau de distribution de contenu](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="d7c8b-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="d7c8b-107">Le tag Helper Link vous permet de spécifier un CDN pour le fichier CSS et une solution de secours lorsque le CDN n’est pas disponible.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="d7c8b-108">Le tag Helper Link offre l’avantage en termes de performances d’un CDN avec la robustesse de l’hébergement local.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="d7c8b-109">Le :::no-loc(Razor)::: balisage suivant montre l' `head` élément d’un fichier de disposition créé avec le modèle d’application Web ASP.net Core :</span><span class="sxs-lookup"><span data-stu-id="d7c8b-109">The following :::no-loc(Razor)::: markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="d7c8b-110">Voici un rendu HTML à partir du code précédent (dans un environnement de non-développement) :</span><span class="sxs-lookup"><span data-stu-id="d7c8b-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="d7c8b-111">Dans le code précédent, le tag Helper Link a généré l' `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` élément et le code JavaScript suivant, qui est utilisé pour vérifier que le fichier *bootstrap. min. CSS* demandé est disponible sur le CDN.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="d7c8b-112">Dans ce cas, le fichier CSS était disponible afin que le tag Helper ait généré l' `<link />` élément avec le fichier CSS CDN.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="d7c8b-113">Attributs d’assistance de balise de lien couramment utilisés</span><span class="sxs-lookup"><span data-stu-id="d7c8b-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="d7c8b-114">Pour plus d’informations sur les attributs, les propriétés et les méthodes de lien tag Helper, consultez [tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  de lien.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="d7c8b-115">href</span><span class="sxs-lookup"><span data-stu-id="d7c8b-115">href</span></span>

<span data-ttu-id="d7c8b-116">Adresse préférée de la ressource liée.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="d7c8b-117">Dans tous les cas, l’adresse est passée au code HTML généré.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="d7c8b-118">ASP-Fallback-href</span><span class="sxs-lookup"><span data-stu-id="d7c8b-118">asp-fallback-href</span></span>

<span data-ttu-id="d7c8b-119">URL d’une feuille de style CSS vers laquelle effectuer le secours dans le cas où l’URL principale échoue.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="d7c8b-120">ASP-Fallback-test-Class</span><span class="sxs-lookup"><span data-stu-id="d7c8b-120">asp-fallback-test-class</span></span>

<span data-ttu-id="d7c8b-121">Nom de classe défini dans la feuille de style à utiliser pour le test de secours.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="d7c8b-122">Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="d7c8b-123">ASP-Fallback-test-propriété</span><span class="sxs-lookup"><span data-stu-id="d7c8b-123">asp-fallback-test-property</span></span>

<span data-ttu-id="d7c8b-124">Nom de la propriété CSS à utiliser pour le test de secours.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="d7c8b-125">Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="d7c8b-126">ASP-Fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="d7c8b-126">asp-fallback-test-value</span></span>

<span data-ttu-id="d7c8b-127">Valeur de propriété CSS à utiliser pour le test de secours.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="d7c8b-128">Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="d7c8b-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d7c8b-129">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d7c8b-129">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
