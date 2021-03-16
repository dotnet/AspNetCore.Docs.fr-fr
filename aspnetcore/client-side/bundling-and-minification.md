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
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Regrouper et réduire les ressources statiques dans ASP.NET Core

Par [Scott Addie](https://twitter.com/Scott_Addie) et [David pin](https://twitter.com/davidpine7)

Cet article explique les avantages de l’application du regroupement et de la minimisation, notamment la façon dont ces fonctionnalités peuvent être utilisées avec ASP.NET Core Web Apps.

## <a name="what-is-bundling-and-minification"></a>Qu’est-ce que le regroupement et la minimisation ?

Le regroupement et la minimisation sont deux optimisations de performances distinctes que vous pouvez appliquer dans une application Web. Utilisés ensemble, le regroupement et la minimisation améliorent les performances en réduisant le nombre de demandes de serveur et en réduisant la taille des ressources statiques demandées.

Le regroupement et la minimisation améliorent principalement le temps de chargement de la première page de la demande. Une fois qu’une page Web a été demandée, le navigateur met en cache les ressources statiques (JavaScript, CSS et images). Ainsi, le regroupement et la minimisation n’améliorent pas les performances lorsque vous demandez la même page ou les mêmes pages sur le même site demandant les mêmes ressources. Si l’en-tête Expires n’est pas défini correctement sur les ressources et si le regroupement et la minimisation ne sont pas utilisés, les heuristiques d’actualisation du navigateur marquent les ressources obsolètes après quelques jours. En outre, le navigateur requiert une demande de validation pour chaque ressource. Dans ce cas, le regroupement et la minimisation améliorent les performances même après la première demande de page.

### <a name="bundling"></a>Regroupement

Le regroupement consiste à combiner plusieurs fichiers en un seul. Le regroupement réduit le nombre de demandes de serveur nécessaires pour afficher une ressource Web, telle qu’une page Web. Vous pouvez créer un nombre quelconque de regroupements individuels spécifiquement pour CSS, JavaScript, etc. Moins de fichiers représentent moins de requêtes HTTP du navigateur vers le serveur ou à partir du service qui fournit votre application. Cela a pour effet d’améliorer les performances de chargement de la première page.

### <a name="minification"></a>Minimisation

La minimisation supprime les caractères inutiles du code sans modifier la fonctionnalité. Le résultat est une réduction significative de la taille des ressources demandées (telles que CSS, les images et les fichiers JavaScript). Les effets secondaires communs de la minimisation incluent la réduction des noms de variables à un caractère et la suppression des commentaires et des espaces inutiles.

Considérons la fonction JavaScript suivante :

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

La minimisation réduit la fonction à ce qui suit :

```javascript
AddAltToImg=function(t,a){var r=$(t,a);r.attr("alt",r.attr("id").replace(/ID/,""))};
```

En plus de supprimer les commentaires et les espaces superflus, les noms de paramètres et de variables suivants ont été renommés comme suit :

Original | Affectation d'un nouveau nom
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>Impact du regroupement et de la minimisation

Le tableau suivant présente les différences entre le chargement individuel des ressources et l’utilisation du regroupement et de la minimisation :

Action | Avec B/M | Sans B/M | Modifier
--- | :---: | :---: | :---:
Demandes de fichier  | 7   | 18     | 157%
Ko transférés | 156 | 264,68 | 70 %
Temps de chargement (MS) | 885 | 2360   | 167%

Les navigateurs sont relativement détaillés en ce qui concerne les en-têtes de requête HTTP. La mesure Total octets envoyés a vu une réduction significative lors du regroupement. Le temps de chargement montre une amélioration significative, mais cet exemple s’est exécuté localement. Des gains de performances plus élevés sont réalisés lorsque vous utilisez le regroupement et la minimisation avec les ressources transférées sur un réseau.

## <a name="choose-a-bundling-and-minification-strategy"></a>Choisir une stratégie de regroupement et de minimisation

ASP.NET Core est compatible avec weboptimizer, une solution de regroupement et de minimisation Open source. Pour configurer des instructions et des exemples de projets, consultez [weboptimizer](https://github.com/ligershark/WebOptimizer). ASP.NET Core ne fournit pas de solution de minimisation et de regroupement native.

Des outils tiers, tels que [Gulp](https://gulpjs.com) et [WebPack](https://webpack.js.org), fournissent une automatisation des flux de travail pour le regroupement et la minimisation, ainsi que la déduplication et l’optimisation des images. En utilisant le regroupement et la minimisation au moment du design, les fichiers minimisés sont créés avant le déploiement de l’application. Le regroupement et le minimisation avant le déploiement offrent l’avantage de réduire la charge du serveur. Toutefois, il est important de reconnaître que le regroupement et la minimisation au moment du design augmentent la complexité de la génération et ne fonctionne qu’avec les fichiers statiques.

## <a name="environment-based-bundling-and-minification"></a>Regroupement et minimisation basés sur l’environnement

Il est recommandé d’utiliser les fichiers regroupés et minimisés de votre application dans un environnement de production. Pendant le développement, les fichiers d’origine facilitent le débogage de l’application.

Spécifiez les fichiers à inclure dans vos pages à l’aide du [tag Helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) dans vos vues. Le tag Helper d’environnement affiche uniquement son contenu lorsqu’il s’exécute dans des [environnements](xref:fundamentals/environments)spécifiques.

La `environment` balise suivante affiche les fichiers CSS non traités lors de l’exécution dans l' `Development` environnement :

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

La `environment` balise suivante affiche les fichiers CSS regroupés et minimisés lorsqu’ils s’exécutent dans un environnement autre que `Development` . Par exemple, l’exécution de dans `Production` ou `Staging` déclenche le rendu de ces feuilles de style :

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

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utiliser plusieurs environnements](xref:fundamentals/environments)
* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
