---
title: Lier tag Helper dans ASP.NET Core
author: rick-anderson
ms.author: riande
description: Découvrez les attributs d’assistance de balise de lien ASP.NET Core et le rôle joué par chaque attribut lors de l’extension du comportement de la balise de lien HTML.
ms.custom: mvc
ms.date: 09/24/2019
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
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: 7998cec94ebb56cbe6dbc321f7cb499260d6fe74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059115"
---
# <a name="link-tag-helper-in-aspnet-core"></a>Lier tag Helper dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Le [tag Helper Link](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) génère un lien vers un fichier CSS principal ou de retour. En général, le fichier CSS principal se trouve sur un [réseau de distribution de contenu](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Le tag Helper Link vous permet de spécifier un CDN pour le fichier CSS et une solution de secours lorsque le CDN n’est pas disponible. Le tag Helper Link offre l’avantage en termes de performances d’un CDN avec la robustesse de l’hébergement local.

Le Razor balisage suivant montre l' `head` élément d’un fichier de disposition créé avec le modèle d’application Web ASP.net Core :

[!code-cshtml[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

Voici un rendu HTML à partir du code précédent (dans un environnement de non-développement) :

[!code-html[](link-tag-helper/sample/HtmlPage1.html)]

Dans le code précédent, le tag Helper Link a généré l' `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` élément et le code JavaScript suivant, qui est utilisé pour vérifier que le fichier *bootstrap. min. CSS* demandé est disponible sur le CDN. Dans ce cas, le fichier CSS était disponible afin que le tag Helper ait généré l' `<link />` élément avec le fichier CSS CDN.

## <a name="commonly-used-link-tag-helper-attributes"></a>Attributs d’assistance de balise de lien couramment utilisés

Pour plus d’informations sur les attributs, les propriétés et les méthodes de lien tag Helper, consultez [tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  de lien.

### <a name="href"></a>href

Adresse préférée de la ressource liée. Dans tous les cas, l’adresse est passée au code HTML généré.

### <a name="asp-fallback-href"></a>ASP-Fallback-href

URL d’une feuille de style CSS vers laquelle effectuer le secours dans le cas où l’URL principale échoue.

### <a name="asp-fallback-test-class"></a>ASP-Fallback-test-Class

Nom de classe défini dans la feuille de style à utiliser pour le test de secours. Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.

### <a name="asp-fallback-test-property"></a>ASP-Fallback-test-propriété

Nom de la propriété CSS à utiliser pour le test de secours. Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.

### <a name="asp-fallback-test-value"></a>ASP-Fallback-test-value

Valeur de propriété CSS à utiliser pour le test de secours. Pour plus d'informations, consultez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
