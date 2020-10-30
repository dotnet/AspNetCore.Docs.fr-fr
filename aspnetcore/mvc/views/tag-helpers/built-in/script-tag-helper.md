---
title: Tag Helper de script dans ASP.NET Core
author: rick-anderson
ms.author: riande
description: Découvrez les attributs d’assistance de balise de script ASP.NET Core et le rôle joué par chaque attribut lors de l’extension du comportement de la balise de script HTML.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- appsettings.json
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: f5856bf19681a42551f82bb15c769f192f338b4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053499"
---
# <a name="script-tag-helper-in-aspnet-core"></a>Tag Helper de script dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Le [tag Helper script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) génère un lien vers un fichier de script principal ou de retour. En général, le fichier de script principal se trouve sur un [réseau de distribution de contenu](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Le tag Helper script vous permet de spécifier un CDN pour le fichier de script et un secours lorsque le CDN n’est pas disponible. Le tag Helper script fournit l’avantage en termes de performances d’un CDN avec la robustesse de l’hébergement local.

Le Razor balisage suivant montre un `script` élément avec une solution de secours :

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

N’utilisez pas l' `<script>` attribut [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) de l’élément pour différer le chargement du script CDN. Le tag Helper script rend JavaScript qui exécute immédiatement l’expression [asp-Fallback-test](#asp-fallback-test) . L’expression échoue si le chargement du script CDN est différé.

## <a name="commonly-used-script-tag-helper-attributes"></a>Attributs d’assistance de balise de script couramment utilisés

Consultez [l’aide de balises de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) pour obtenir tous les attributs, propriétés et méthodes du programme d’assistance des balises de script.

### <a name="asp-fallback-test"></a>ASP-Fallback-test

Méthode de script définie dans le script principal à utiliser pour le test de secours. Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.

### <a name="asp-fallback-src"></a>ASP-Fallback-SRC

URL d’une balise de script vers laquelle effectuer un secours dans le cas où le principal échoue. Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
