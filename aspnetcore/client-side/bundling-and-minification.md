---
title: Regrouper et réduire les ressources statiques dans ASP.NET Core
author: scottaddie
description: Découvrez comment optimiser les ressources statiques dans une application Web ASP.NET Core en appliquant des techniques de regroupement et de minimisation.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/14/2021
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: d594bbf277907e22b0299b0451e480e9d533d506
ms.sourcegitcommit: 00368bb6a5420983beaced5b62dabc1f94abdeba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103557801"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a><span data-ttu-id="5e92a-103">Regrouper et réduire les ressources statiques dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e92a-103">Bundle and minify static assets in ASP.NET Core</span></span>

<span data-ttu-id="5e92a-104">Par [Scott Addie](https://twitter.com/Scott_Addie) et [David pin](https://twitter.com/davidpine7)</span><span class="sxs-lookup"><span data-stu-id="5e92a-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [David Pine](https://twitter.com/davidpine7)</span></span>

<span data-ttu-id="5e92a-105">Cet article explique les avantages de l’application du regroupement et de la minimisation, notamment la façon dont ces fonctionnalités peuvent être utilisées avec ASP.NET Core Web Apps.</span><span class="sxs-lookup"><span data-stu-id="5e92a-105">This article explains the benefits of applying bundling and minification, including how these features can be used with ASP.NET Core web apps.</span></span>

## <a name="what-is-bundling-and-minification"></a><span data-ttu-id="5e92a-106">Qu’est-ce que le regroupement et la minimisation ?</span><span class="sxs-lookup"><span data-stu-id="5e92a-106">What is bundling and minification</span></span>

<span data-ttu-id="5e92a-107">Le regroupement et la minimisation sont deux optimisations de performances distinctes que vous pouvez appliquer dans une application Web.</span><span class="sxs-lookup"><span data-stu-id="5e92a-107">Bundling and minification are two distinct performance optimizations you can apply in a web app.</span></span> <span data-ttu-id="5e92a-108">Utilisés ensemble, le regroupement et la minimisation améliorent les performances en réduisant le nombre de demandes de serveur et en réduisant la taille des ressources statiques demandées.</span><span class="sxs-lookup"><span data-stu-id="5e92a-108">Used together, bundling and minification improve performance by reducing the number of server requests and reducing the size of the requested static assets.</span></span>

<span data-ttu-id="5e92a-109">Le regroupement et la minimisation améliorent principalement le temps de chargement de la première page de la demande.</span><span class="sxs-lookup"><span data-stu-id="5e92a-109">Bundling and minification primarily improve the first page request load time.</span></span> <span data-ttu-id="5e92a-110">Une fois qu’une page Web a été demandée, le navigateur met en cache les ressources statiques (JavaScript, CSS et images).</span><span class="sxs-lookup"><span data-stu-id="5e92a-110">Once a web page has been requested, the browser caches the static assets (JavaScript, CSS, and images).</span></span> <span data-ttu-id="5e92a-111">Ainsi, le regroupement et la minimisation n’améliorent pas les performances lorsque vous demandez la même page ou les mêmes pages sur le même site demandant les mêmes ressources.</span><span class="sxs-lookup"><span data-stu-id="5e92a-111">So, bundling and minification don't improve performance when requesting the same page, or pages, on the same site requesting the same assets.</span></span> <span data-ttu-id="5e92a-112">Si l’en-tête Expires n’est pas défini correctement sur les ressources et si le regroupement et la minimisation ne sont pas utilisés, les heuristiques d’actualisation du navigateur marquent les ressources obsolètes après quelques jours.</span><span class="sxs-lookup"><span data-stu-id="5e92a-112">If the expires header isn't set correctly on the assets and if bundling and minification isn't used, the browser's freshness heuristics mark the assets stale after a few days.</span></span> <span data-ttu-id="5e92a-113">En outre, le navigateur requiert une demande de validation pour chaque ressource.</span><span class="sxs-lookup"><span data-stu-id="5e92a-113">Additionally, the browser requires a validation request for each asset.</span></span> <span data-ttu-id="5e92a-114">Dans ce cas, le regroupement et la minimisation améliorent les performances même après la première demande de page.</span><span class="sxs-lookup"><span data-stu-id="5e92a-114">In this case, bundling and minification provide a performance improvement even after the first page request.</span></span>

### <a name="bundling"></a><span data-ttu-id="5e92a-115">Regroupement</span><span class="sxs-lookup"><span data-stu-id="5e92a-115">Bundling</span></span>

<span data-ttu-id="5e92a-116">Le regroupement consiste à combiner plusieurs fichiers en un seul.</span><span class="sxs-lookup"><span data-stu-id="5e92a-116">Bundling combines multiple files into a single file.</span></span> <span data-ttu-id="5e92a-117">Le regroupement réduit le nombre de demandes de serveur nécessaires pour afficher une ressource Web, telle qu’une page Web.</span><span class="sxs-lookup"><span data-stu-id="5e92a-117">Bundling reduces the number of server requests that are necessary to render a web asset, such as a web page.</span></span> <span data-ttu-id="5e92a-118">Vous pouvez créer un nombre quelconque de regroupements individuels spécifiquement pour CSS, JavaScript, etc. Moins de fichiers représentent moins de requêtes HTTP du navigateur vers le serveur ou à partir du service qui fournit votre application.</span><span class="sxs-lookup"><span data-stu-id="5e92a-118">You can create any number of individual bundles specifically for CSS, JavaScript, etc. Fewer files mean fewer HTTP requests from the browser to the server or from the service providing your application.</span></span> <span data-ttu-id="5e92a-119">Cela a pour effet d’améliorer les performances de chargement de la première page.</span><span class="sxs-lookup"><span data-stu-id="5e92a-119">This results in improved first page load performance.</span></span>

### <a name="minification"></a><span data-ttu-id="5e92a-120">Minimisation</span><span class="sxs-lookup"><span data-stu-id="5e92a-120">Minification</span></span>

<span data-ttu-id="5e92a-121">La minimisation supprime les caractères inutiles du code sans modifier la fonctionnalité.</span><span class="sxs-lookup"><span data-stu-id="5e92a-121">Minification removes unnecessary characters from code without altering functionality.</span></span> <span data-ttu-id="5e92a-122">Le résultat est une réduction significative de la taille des ressources demandées (telles que CSS, les images et les fichiers JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5e92a-122">The result is a significant size reduction in requested assets (such as CSS, images, and JavaScript files).</span></span> <span data-ttu-id="5e92a-123">Les effets secondaires communs de la minimisation incluent la réduction des noms de variables à un caractère et la suppression des commentaires et des espaces inutiles.</span><span class="sxs-lookup"><span data-stu-id="5e92a-123">Common side effects of minification include shortening variable names to one character and removing comments and unnecessary whitespace.</span></span>

<span data-ttu-id="5e92a-124">Considérons la fonction JavaScript suivante :</span><span class="sxs-lookup"><span data-stu-id="5e92a-124">Consider the following JavaScript function:</span></span>

```javascript
AddAltToImg = function (imageTagAndImageID, imageContext) {
    ///<signature>
    ///<summary> Adds an alt tab to the image
    // </summary>
    //<param name="imgElement" type="String">The image selector.</param>
    //<param name="ContextForImage" type="String">The image context.</param>
    ///</signature>
    var imageElement = $(imageTagAndImageID, imageContext);
    imageElement.attr('alt', imageElement.attr('id').replace(/ID/, ''));
}
```

<span data-ttu-id="5e92a-125">La minimisation réduit la fonction à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="5e92a-125">Minification reduces the function to the following:</span></span>

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

<span data-ttu-id="5e92a-126">En plus de supprimer les commentaires et les espaces superflus, les noms de paramètres et de variables suivants ont été renommés comme suit :</span><span class="sxs-lookup"><span data-stu-id="5e92a-126">In addition to removing the comments and unnecessary whitespace, the following parameter and variable names were renamed as follows:</span></span>

<span data-ttu-id="5e92a-127">Original</span><span class="sxs-lookup"><span data-stu-id="5e92a-127">Original</span></span> | <span data-ttu-id="5e92a-128">Affectation d'un nouveau nom</span><span class="sxs-lookup"><span data-stu-id="5e92a-128">Renamed</span></span>
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a><span data-ttu-id="5e92a-129">Impact du regroupement et de la minimisation</span><span class="sxs-lookup"><span data-stu-id="5e92a-129">Impact of bundling and minification</span></span>

<span data-ttu-id="5e92a-130">Le tableau suivant présente les différences entre le chargement individuel des ressources et l’utilisation du regroupement et de la minimisation :</span><span class="sxs-lookup"><span data-stu-id="5e92a-130">The following table outlines differences between individually loading assets and using bundling and minification:</span></span>

<span data-ttu-id="5e92a-131">Action</span><span class="sxs-lookup"><span data-stu-id="5e92a-131">Action</span></span> | <span data-ttu-id="5e92a-132">Avec B/M</span><span class="sxs-lookup"><span data-stu-id="5e92a-132">With B/M</span></span> | <span data-ttu-id="5e92a-133">Sans B/M</span><span class="sxs-lookup"><span data-stu-id="5e92a-133">Without B/M</span></span> | <span data-ttu-id="5e92a-134">Modifier</span><span class="sxs-lookup"><span data-stu-id="5e92a-134">Change</span></span>
--- | :---: | :---: | :---:
<span data-ttu-id="5e92a-135">Demandes de fichier</span><span class="sxs-lookup"><span data-stu-id="5e92a-135">File Requests</span></span>  | <span data-ttu-id="5e92a-136">7</span><span class="sxs-lookup"><span data-stu-id="5e92a-136">7</span></span>   | <span data-ttu-id="5e92a-137">18</span><span class="sxs-lookup"><span data-stu-id="5e92a-137">18</span></span>     | <span data-ttu-id="5e92a-138">157%</span><span class="sxs-lookup"><span data-stu-id="5e92a-138">157%</span></span>
<span data-ttu-id="5e92a-139">Ko transférés</span><span class="sxs-lookup"><span data-stu-id="5e92a-139">KB Transferred</span></span> | <span data-ttu-id="5e92a-140">156</span><span class="sxs-lookup"><span data-stu-id="5e92a-140">156</span></span> | <span data-ttu-id="5e92a-141">264,68</span><span class="sxs-lookup"><span data-stu-id="5e92a-141">264.68</span></span> | <span data-ttu-id="5e92a-142">70 %</span><span class="sxs-lookup"><span data-stu-id="5e92a-142">70%</span></span>
<span data-ttu-id="5e92a-143">Temps de chargement (MS)</span><span class="sxs-lookup"><span data-stu-id="5e92a-143">Load Time (ms)</span></span> | <span data-ttu-id="5e92a-144">885</span><span class="sxs-lookup"><span data-stu-id="5e92a-144">885</span></span> | <span data-ttu-id="5e92a-145">2360</span><span class="sxs-lookup"><span data-stu-id="5e92a-145">2360</span></span>   | <span data-ttu-id="5e92a-146">167%</span><span class="sxs-lookup"><span data-stu-id="5e92a-146">167%</span></span>

<span data-ttu-id="5e92a-147">Les navigateurs sont relativement détaillés en ce qui concerne les en-têtes de requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="5e92a-147">Browsers are fairly verbose regarding HTTP request headers.</span></span> <span data-ttu-id="5e92a-148">La mesure Total octets envoyés a vu une réduction significative lors du regroupement.</span><span class="sxs-lookup"><span data-stu-id="5e92a-148">The total bytes sent metric saw a significant reduction when bundling.</span></span> <span data-ttu-id="5e92a-149">Le temps de chargement montre une amélioration significative, mais cet exemple s’est exécuté localement.</span><span class="sxs-lookup"><span data-stu-id="5e92a-149">The load time shows a significant improvement, however this example ran locally.</span></span> <span data-ttu-id="5e92a-150">Des gains de performances plus élevés sont réalisés lorsque vous utilisez le regroupement et la minimisation avec les ressources transférées sur un réseau.</span><span class="sxs-lookup"><span data-stu-id="5e92a-150">Greater performance gains are realized when using bundling and minification with assets transferred over a network.</span></span>

## <a name="choose-a-bundling-and-minification-strategy"></a><span data-ttu-id="5e92a-151">Choisir une stratégie de regroupement et de minimisation</span><span class="sxs-lookup"><span data-stu-id="5e92a-151">Choose a bundling and minification strategy</span></span>

<span data-ttu-id="5e92a-152">ASP.NET Core est compatible avec weboptimizer, une solution de regroupement et de minimisation Open source.</span><span class="sxs-lookup"><span data-stu-id="5e92a-152">ASP.NET Core is compatible with WebOptimizer, an open-source bundling and minification solution.</span></span> <span data-ttu-id="5e92a-153">Pour configurer des instructions et des exemples de projets, consultez [weboptimizer](https://github.com/ligershark/WebOptimizer).</span><span class="sxs-lookup"><span data-stu-id="5e92a-153">For set up instructions and sample projects, see [WebOptimizer](https://github.com/ligershark/WebOptimizer).</span></span> <span data-ttu-id="5e92a-154">ASP.NET Core ne fournit pas de solution de minimisation et de regroupement native.</span><span class="sxs-lookup"><span data-stu-id="5e92a-154">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span>

<span data-ttu-id="5e92a-155">Des outils tiers, tels que [Gulp](https://gulpjs.com) et [WebPack](https://webpack.js.org), fournissent une automatisation des flux de travail pour le regroupement et la minimisation, ainsi que la déduplication et l’optimisation des images.</span><span class="sxs-lookup"><span data-stu-id="5e92a-155">Third-party tools, such as [Gulp](https://gulpjs.com) and [Webpack](https://webpack.js.org), provide workflow automation for bundling and minification, as well as linting and image optimization.</span></span> <span data-ttu-id="5e92a-156">En utilisant le regroupement et la minimisation au moment du design, les fichiers minimisés sont créés avant le déploiement de l’application.</span><span class="sxs-lookup"><span data-stu-id="5e92a-156">By using design-time bundling and minification, the minified files are created prior to the app's deployment.</span></span> <span data-ttu-id="5e92a-157">Le regroupement et le minimisation avant le déploiement offrent l’avantage de réduire la charge du serveur.</span><span class="sxs-lookup"><span data-stu-id="5e92a-157">Bundling and minifying before deployment provides the advantage of reduced server load.</span></span> <span data-ttu-id="5e92a-158">Toutefois, il est important de reconnaître que le regroupement et la minimisation au moment du design augmentent la complexité de la génération et ne fonctionne qu’avec les fichiers statiques.</span><span class="sxs-lookup"><span data-stu-id="5e92a-158">However, it's important to recognize that design-time bundling and minification increases build complexity and only works with static files.</span></span>

## <a name="environment-based-bundling-and-minification"></a><span data-ttu-id="5e92a-159">Regroupement et minimisation basés sur l’environnement</span><span class="sxs-lookup"><span data-stu-id="5e92a-159">Environment-based bundling and minification</span></span>

<span data-ttu-id="5e92a-160">Il est recommandé d’utiliser les fichiers regroupés et minimisés de votre application dans un environnement de production.</span><span class="sxs-lookup"><span data-stu-id="5e92a-160">As a best practice, the bundled and minified files of your app should be used in a production environment.</span></span> <span data-ttu-id="5e92a-161">Pendant le développement, les fichiers d’origine facilitent le débogage de l’application.</span><span class="sxs-lookup"><span data-stu-id="5e92a-161">During development, the original files make for easier debugging of the app.</span></span>

<span data-ttu-id="5e92a-162">Spécifiez les fichiers à inclure dans vos pages à l’aide du [tag Helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) dans vos vues.</span><span class="sxs-lookup"><span data-stu-id="5e92a-162">Specify which files to include in your pages by using the [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) in your views.</span></span> <span data-ttu-id="5e92a-163">Le tag Helper d’environnement affiche uniquement son contenu lorsqu’il s’exécute dans des [environnements](xref:fundamentals/environments)spécifiques.</span><span class="sxs-lookup"><span data-stu-id="5e92a-163">The Environment Tag Helper only renders its contents when running in specific [environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="5e92a-164">La `environment` balise suivante affiche les fichiers CSS non traités lors de l’exécution dans l' `Development` environnement :</span><span class="sxs-lookup"><span data-stu-id="5e92a-164">The following `environment` tag renders the unprocessed CSS files when running in the `Development` environment:</span></span>

::: moniker range=">= aspnetcore-2.0"

```cshtml
<environment include="Development">
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
    <link rel="stylesheet" href="~/css/site.css" />
</environment>
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```cshtml
<environment names="Staging,Production">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

<span data-ttu-id="5e92a-165">La `environment` balise suivante affiche les fichiers CSS regroupés et minimisés lorsqu’ils s’exécutent dans un environnement autre que `Development` .</span><span class="sxs-lookup"><span data-stu-id="5e92a-165">The following `environment` tag renders the bundled and minified CSS files when running in an environment other than `Development`.</span></span> <span data-ttu-id="5e92a-166">Par exemple, l’exécution de dans `Production` ou `Staging` déclenche le rendu de ces feuilles de style :</span><span class="sxs-lookup"><span data-stu-id="5e92a-166">For example, running in `Production` or `Staging` triggers the rendering of these stylesheets:</span></span>

::: moniker range=">= aspnetcore-2.0"

```cshtml
<environment exclude="Development">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```cshtml
<environment names="Staging,Production">
    <link rel="stylesheet" href="https://ajax.aspnetcdn.com/ajax/bootstrap/3.3.7/css/bootstrap.min.css"
          asp-fallback-href="~/lib/bootstrap/dist/css/bootstrap.min.css"
          asp-fallback-test-class="sr-only" asp-fallback-test-property="position" asp-fallback-test-value="absolute" />
    <link rel="stylesheet" href="~/css/site.min.css" asp-append-version="true" />
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5e92a-167">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5e92a-167">Additional resources</span></span>

* [<span data-ttu-id="5e92a-168">Utiliser plusieurs environnements</span><span class="sxs-lookup"><span data-stu-id="5e92a-168">Use multiple environments</span></span>](xref:fundamentals/environments)
* [<span data-ttu-id="5e92a-169">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="5e92a-169">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
