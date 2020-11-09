---
title: Tag Helper Environnement dans ASP.NET Core
author: pkellner
description: Tag Helper Environnement ASP.NET Core défini avec toutes les propriétés
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 4f8330521bb9114f6639c1889cc15ebd18adc0ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061130"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="7ea28-103">Tag Helper Environnement dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ea28-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="7ea28-104">Par [Peter Kellner](https://peterkellner.net) et [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="7ea28-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="7ea28-105">Le tag Helper d’environnement affiche de manière conditionnelle son contenu délimité en fonction de l' [environnement d’hébergement](xref:fundamentals/environments)actuel.</span><span class="sxs-lookup"><span data-stu-id="7ea28-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="7ea28-106">L’attribut unique du Tag Helper Environnement, `names`, est une liste séparée par des virgules de noms d’environnement.</span><span class="sxs-lookup"><span data-stu-id="7ea28-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="7ea28-107">Si l’un des noms d’environnement fournis correspond à l’environnement actuel, le contenu joint est affiché.</span><span class="sxs-lookup"><span data-stu-id="7ea28-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="7ea28-108">Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="7ea28-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="7ea28-109">Attributs de Tag Helper Environnement</span><span class="sxs-lookup"><span data-stu-id="7ea28-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="7ea28-110">noms</span><span class="sxs-lookup"><span data-stu-id="7ea28-110">names</span></span>

<span data-ttu-id="7ea28-111">`names` accepte un seul nom d’environnement d’hébergement ou une liste séparée par des virgules de noms d’environnement d’hébergement qui déclenchent l’affichage du contenu joint.</span><span class="sxs-lookup"><span data-stu-id="7ea28-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="7ea28-112">Les valeurs d’environnement sont comparées à la valeur actuelle retournée par [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="7ea28-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="7ea28-113">La comparaison ignore la casse.</span><span class="sxs-lookup"><span data-stu-id="7ea28-113">The comparison ignores case.</span></span>

<span data-ttu-id="7ea28-114">L’exemple suivant utilise un Tag Helper Environnement.</span><span class="sxs-lookup"><span data-stu-id="7ea28-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="7ea28-115">Le contenu est affiché si l’environnement d’hébergement est un environnement de préproduction (Staging) ou de production :</span><span class="sxs-lookup"><span data-stu-id="7ea28-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="7ea28-116">Attributs include et exclude</span><span class="sxs-lookup"><span data-stu-id="7ea28-116">include and exclude attributes</span></span>

<span data-ttu-id="7ea28-117">`include`les & `exclude` attributs contrôlent le rendu du contenu délimité en fonction des noms d’environnement d’hébergement inclus ou exclus.</span><span class="sxs-lookup"><span data-stu-id="7ea28-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="7ea28-118">include</span><span class="sxs-lookup"><span data-stu-id="7ea28-118">include</span></span>

<span data-ttu-id="7ea28-119">La propriété `include` présente un comportement similaire à l’attribut `names`.</span><span class="sxs-lookup"><span data-stu-id="7ea28-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="7ea28-120">Un environnement listé dans la valeur d’attribut `include` doit correspondre à l’environnement d’hébergement de l’application ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) pour afficher le contenu de la balise `<environment>`.</span><span class="sxs-lookup"><span data-stu-id="7ea28-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="7ea28-121">exclure</span><span class="sxs-lookup"><span data-stu-id="7ea28-121">exclude</span></span>

<span data-ttu-id="7ea28-122">Contrairement à l’attribut `include`, le contenu de la balise `<environment>` est affiché quand l’environnement d’hébergement ne correspond pas à un environnement listé dans la valeur d’attribut `exclude`.</span><span class="sxs-lookup"><span data-stu-id="7ea28-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7ea28-123">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7ea28-123">Additional resources</span></span>

* <xref:fundamentals/environments>
