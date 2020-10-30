---
title: Tag Helper Partial dans ASP.NET Core
author: scottaddie
description: Découvrez le Tag Helper Partial ASP.NET Core et le rôle de ses attributs dans le rendu d’une vue partielle.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 124f23caa4a757f63a80dfea627304204ba2cdca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061429"
---
# <a name="partial-tag-helper-in-aspnet-core"></a>Tag Helper Partial dans ASP.NET Core

Par [Scott Addie](https://github.com/scottaddie)

Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="overview"></a>Vue d'ensemble

Le tag Helper partiel est utilisé pour restituer une [vue partielle](xref:mvc/views/partial) dans Razor les pages et les applications MVC. Tenez compte des points suivants :

* Il nécessite ASP.NET Core 2.1 ou ultérieur.
* Il constitue une alternative à la [syntaxe HTML Helper](xref:mvc/views/partial#reference-a-partial-view).
* Il affiche la vue partielle de façon asynchrone.

Parmi les options HTML Helper utilisées pour le rendu d’une vue partielle, citons les suivantes :

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

Le modèle *Product* est utilisé dans les exemples tout au long de ce document :

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

Voici l’inventaire des attributs du Tag Helper Partial.

## <a name="name"></a>name

L'attribut `name` est obligatoire. Il indique le nom ou le chemin de la vue partielle à afficher. Quand un nom de vue partielle est fourni, le processus de [découverte de vue](xref:mvc/views/overview#view-discovery) est lancé. Ce processus est ignoré quand un chemin explicite est fourni. Pour connaître toutes les valeurs `name` acceptables, consultez [Découverte des vues partielles](xref:mvc/views/partial#partial-view-discovery).

Le balisage suivant utilise un chemin explicite indiquant que le fichier *_ProductPartial.cshtml* doit être chargé à partir du dossier *Shared* . À l’aide de l’attribut [for](#for), un modèle est passé à la vue partielle pour liaison.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a>pour

L’attribut `for` affecte un [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) à évaluer par rapport au modèle actif. `ModelExpression` déduit la syntaxe `@Model.`. Par exemple, `for="Product"` peut être utilisé à la place de `for="@Model.Product"`. Pour substituer ce comportement d’inférence par défaut, utilisez le symbole `@` pour définir une expression inline.

Le balisage suivant charge *_ProductPartial.cshtml* :

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

La vue partielle est liée à la propriété `Product` du modèle de page associé :

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a>model

L’attribut `model` affecte une instance de modèle à passer à la vue partielle. L’attribut `model` ne peut pas être utilisé avec l’attribut [for](#for).

Dans le balisage suivant, un nouvel objet `Product` est instancié et passé à l’attribut `model` pour liaison :

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a>view-data

L’attribut `view-data` affecte un [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) à passer à la vue partielle. Le balisage suivant rend l’ensemble de la collection ViewData accessible à la vue partielle :

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

Dans le code précédent, la valeur de clé `IsNumberReadOnly` est `true` et ajoutée à la collection ViewData. `ViewData["IsNumberReadOnly"]` est donc accessible au sein de la vue partielle suivante :

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

Dans cet exemple, la valeur de `ViewData["IsNumberReadOnly"]` détermine si le champ *Number* s’affiche en lecture seule.

## <a name="migrate-from-an-html-helper"></a>Migrer à partir d’une assistance HTML

Prenons l’exemple d’assistance HTML asynchrone suivante. Une collection de produits est parcourue et affichée. Conformément au premier paramètre de la méthode `PartialAsync`, la vue partielle *_ProductPartial.cshtml* est chargée. Une instance du modèle `Product` est passée à la vue partielle pour la liaison.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

Le Tag Helper Partial suivant permet d’obtenir le même comportement de rendu asynchrone que l’assistance HTML `PartialAsync`. Une instance de modèle `Product` est assignée à l’attribut `model` pour la liaison à la vue partielle.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
