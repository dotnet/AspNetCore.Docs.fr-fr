---
title: Regrouper et réduire les ressources statiques dans ASP.NET Core
author: scottaddie
description: Découvrez comment optimiser les ressources statiques dans une application Web ASP.NET Core en appliquant des techniques de regroupement et de minimisation.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/02/2020
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
ms.openlocfilehash: 7dd11ceb7a7c01ce1042f50595013b7fe7f1cd5c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054838"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>Regrouper et réduire les ressources statiques dans ASP.NET Core

Par [Scott Addie](https://twitter.com/Scott_Addie) et [David pin](https://twitter.com/davidpine7)

Cet article explique les avantages de l’application du regroupement et de la minimisation, notamment la façon dont ces fonctionnalités peuvent être utilisées avec ASP.NET Core Web Apps.

## <a name="what-is-bundling-and-minification"></a>Qu’est-ce que le regroupement et la minimisation ?

Le regroupement et la minimisation sont deux optimisations de performances distinctes que vous pouvez appliquer dans une application Web. Utilisés ensemble, le regroupement et la minimisation améliorent les performances en réduisant le nombre de demandes de serveur et en réduisant la taille des ressources statiques demandées.

Le regroupement et la minimisation améliorent principalement le temps de chargement de la première page de la demande. Une fois qu’une page Web a été demandée, le navigateur met en cache les ressources statiques (JavaScript, CSS et images). Par conséquent, le regroupement et la minimisation n’améliorent pas les performances lorsque vous demandez la même page ou les mêmes pages sur le même site demandant les mêmes ressources. Si l’en-tête Expires n’est pas défini correctement sur les ressources et si le regroupement et la minimisation ne sont pas utilisés, les heuristiques d’actualisation du navigateur marquent les ressources obsolètes après quelques jours. En outre, le navigateur requiert une demande de validation pour chaque ressource. Dans ce cas, le regroupement et la minimisation améliorent les performances même après la première demande de page.

### <a name="bundling"></a>Regroupement

Le regroupement consiste à combiner plusieurs fichiers en un seul. Le regroupement réduit le nombre de demandes de serveur nécessaires pour afficher une ressource Web, telle qu’une page Web. Vous pouvez créer un nombre quelconque de regroupements individuels spécifiquement pour CSS, JavaScript, etc. Moins de fichiers signifie moins de demandes HTTP du navigateur vers le serveur ou à partir du service qui fournit votre application. Cela a pour effet d’améliorer les performances de chargement de la première page.

### <a name="minification"></a>Minimisation

La minimisation supprime les caractères inutiles du code sans modifier la fonctionnalité. Le résultat est une réduction significative de la taille des ressources demandées (telles que CSS, les images et les fichiers JavaScript). Les effets secondaires communs de la minimisation incluent la réduction des noms de variables à un caractère et la suppression des commentaires et des espaces inutiles.

Considérons la fonction JavaScript suivante :

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

La minimisation réduit la fonction à ce qui suit :

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

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

Les modèles de Razor projet MVC et pages fournissent une solution pour le regroupement et la minimisation consistant en un fichier de configuration JSON. Des outils tiers, tels que le testeur de tâches [grunt](xref:client-side/using-grunt) , accomplissent les mêmes tâches avec un peu plus de complexité. Un outil tiers est une solution idéale lorsque votre flux de travail de développement nécessite un traitement au-delà du regroupement et de la minimisation &mdash; , tels que le tissu et l’optimisation d’image. En utilisant le regroupement et la minimisation au moment du design, les fichiers minimisés sont créés avant le déploiement de l’application. Le regroupement et le minimisation avant le déploiement offrent l’avantage de réduire la charge du serveur. Toutefois, il est important de reconnaître que le regroupement et la minimisation au moment du design augmentent la complexité de la génération et ne fonctionne qu’avec les fichiers statiques.

## <a name="configure-bundling-and-minification"></a>Configurer le regroupement et la minimisation

> [!NOTE]
> Pour que cela fonctionne, le package NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) doit être ajouté à votre projet.

::: moniker range="<= aspnetcore-2.0"

Dans ASP.NET Core 2,0 ou version antérieure, les modèles de projet MVC et Razor pages fournissent un *bundleconfig.jssur* le fichier de configuration qui définit les options pour chaque Bundle :

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Dans ASP.NET Core 2,1 ou version ultérieure, ajoutez un nouveau fichier JSON, nommé *bundleconfig.jssur*, à la racine du projet MVC ou Razor pages. Incluez le code JSON suivant dans ce fichier comme point de départ :

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

Le *bundleconfig.jssur* le fichier définit les options pour chaque bundle. Dans l’exemple précédent, une configuration de regroupement unique est définie pour les fichiers JavaScript personnalisés (*wwwroot/js/site.js*) et StyleSheet (*wwwroot/CSS/site. CSS*).

Les options de configuration comprennent ce qui suit :

* `outputFileName`: Nom du fichier de Bundle à générer. Peut contenir un chemin d’accès relatif à partir de l' *bundleconfig.jssur* le fichier. **Obligatoire**
* `inputFiles`: Tableau de fichiers à regrouper. Il s’agit de chemins d’accès relatifs au fichier de configuration. **facultatif**, * une valeur vide génère un fichier de sortie vide. les modèles [globbing](https://www.tldp.org/LDP/abs/html/globbingref.html) sont pris en charge.
* `minify`: Options de minimisation pour le type de sortie. **facultatif**, *par défaut `minify: { enabled: true }` :*
  * Les options de configuration sont disponibles pour chaque type de fichier de sortie.
    * [Minifier CSS](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [Minifier JavaScript](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [Minifier HTML](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`: Indicateur précisant s’il faut ajouter les fichiers générés au fichier projet. **facultatif**, *valeur par défaut-false*
* `sourceMap`: Indicateur précisant s’il faut générer un mappage source pour le fichier groupé. **facultatif**, *valeur par défaut-false*
* `sourceMapRootPath`: Chemin d’accès racine pour le stockage du fichier de mappage source généré.

## <a name="add-files-to-workflow"></a>Ajouter des fichiers au flux de travail

Prenons l’exemple d’un fichier *. CSS personnalisé* supplémentaire qui ressemble à ce qui suit :

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

Pour réduire *Custom. CSS* et le regrouper avec *site. CSS* dans un fichier *site. min. CSS* , ajoutez le chemin d’accès relatif à *bundleconfig.jssur*:

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> Vous pouvez également utiliser le modèle globbing suivant :
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> Ce modèle globbing correspond à tous les fichiers CSS et exclut le modèle de fichier minimisés.

Générez l’application. Ouvrez *site. min. CSS* et notez que le contenu du fichier *Custom. CSS* est ajouté à la fin du fichier.

## <a name="environment-based-bundling-and-minification"></a>Regroupement et minimisation basés sur l’environnement

Il est recommandé d’utiliser les fichiers regroupés et minimisés de votre application dans un environnement de production. Pendant le développement, les fichiers d’origine facilitent le débogage de l’application.

Spécifiez les fichiers à inclure dans vos pages à l’aide du [tag Helper d’environnement](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) dans vos vues. Le tag Helper d’environnement affiche uniquement son contenu lorsqu’il s’exécute dans des [environnements](xref:fundamentals/environments)spécifiques.

La `environment` balise suivante affiche les fichiers CSS non traités lors de l’exécution dans l' `Development` environnement :

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

La `environment` balise suivante affiche les fichiers CSS regroupés et minimisés lorsqu’ils s’exécutent dans un environnement autre que `Development` . Par exemple, l’exécution de dans `Production` ou `Staging` déclenche le rendu de ces feuilles de style :

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>Utiliser bundleconfig.jsà partir de Gulp

Dans certains cas, le flux de travail de regroupement et de minimisation d’une application nécessite un traitement supplémentaire. Les exemples incluent l’optimisation des images, la combustion du cache et le traitement des ressources CDN. Pour répondre à ces exigences, vous pouvez convertir le flux de travail de regroupement et de minimisation pour utiliser Gulp.

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>Convertir manuellement le flux de travail de regroupement et de minimisation pour utiliser Gulp

Ajoutez un *package.jssur* le fichier, avec les éléments suivants `devDependencies` , à la racine du projet :

> [!WARNING]
> Le `gulp-uglify` module ne prend pas en charge ECMAScript (es) 2015/ES6 et versions ultérieures. Installez [Gulp-terser](https://www.npmjs.com/package/gulp-terser) au lieu de `gulp-uglify` pour utiliser ES2015/ES6 ou une version ultérieure.

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

Installez les dépendances en exécutant la commande suivante au même niveau que *package.jssur*:

```bash
npm i
```

Installez Gulp CLI en tant que dépendance globale :

```bash
npm i -g gulp-cli
```

Copiez le fichier *gulpfile.js* ci-dessous sur la racine du projet :

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>Exécuter des tâches Gulp

Pour déclencher la tâche de minimisation Gulp avant la génération du projet dans Visual Studio :

1. Installez le package NuGet [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) .
1. Ajoutez la [cible MSBuild](/visualstudio/msbuild/msbuild-targets) suivante au fichier projet :

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

Dans cet exemple, toutes les tâches définies dans la `MyPreCompileTarget` cible s’exécutent avant la cible prédéfinie `Build` . Une sortie similaire à ce qui suit apparaît dans la fenêtre sortie de Visual Studio :

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Utiliser Grunt](xref:client-side/using-grunt)
* [Utiliser plusieurs environnements](xref:fundamentals/environments)
* [Tag Helpers](xref:mvc/views/tag-helpers/intro)
