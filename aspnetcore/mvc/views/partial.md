---
title: Vues partielles dans ASP.NET Core
author: ardalis
description: Découvrez comment utiliser les vues partielles pour découper des fichiers de balisage volumineux et réduire la duplication du balisage commun entre les différentes pages web dans les applications ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: 3a7b054adc50be57028dfa0364f80ae8733b02f7
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252706"
---
# <a name="partial-views-in-aspnet-core"></a>Vues partielles dans ASP.NET Core

Par [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Scott Sauber](https://twitter.com/scottsauber)

Une vue partielle est un [Razor](xref:mvc/views/razor) fichier de balisage (*. cshtml*) sans [`@page`](xref:mvc/views/razor#page) directive qui restitue la sortie HTML *dans* la sortie rendue d’un autre fichier de balisage.

::: moniker range=">= aspnetcore-2.1"

Le terme *vue partielle* est utilisé lors du développement d’une application MVC, où les fichiers de balisage sont appelés *vues* ou Razor application pages, où les fichiers de balisage sont appelés *pages*. Cette rubrique fait référence aux pages vues et Razor pages MVC en tant que *fichiers de balisage*.

::: moniker-end

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="when-to-use-partial-views"></a>Quand utiliser des vues partielles ?

Les vues partielles sont utiles pour :

* Découper des fichiers de balisage volumineux en composants plus petits.

  Dans un grand fichier de balisage complexe composé de plusieurs parties logiques, il peut s’avérer utile de travailler sur chaque partie isolée dans une vue partielle. Le code dans le fichier de balisage est facile à gérer, puisqu’il contient uniquement la structure de page globale et les références aux vues partielles.
* Réduire la duplication du contenu de balisage commun entre les fichiers de balisage.

  Quand les mêmes éléments de balisage sont utilisés entre plusieurs fichiers de balisage, une vue partielle supprime la duplication du contenu de balisage dans un seul fichier de vue partielle. Quand le balisage est modifié dans la vue partielle, il met à jour la sortie rendue des fichiers de balisage qui utilisent cette vue partielle.

Les vues partielles ne doivent pas être utilisées pour tenir à jour des éléments de disposition communs. Ces éléments doivent être spécifiés dans des fichiers [_Layout.cshtml](xref:mvc/views/layout).

N’utilisez pas une vue partielle quand du code ou une logique de rendu complexe doit être exécuté pour effectuer le rendu du balisage. Dans ce cas, utilisez un [composant de vue](xref:mvc/views/view-components) à la place.

## <a name="declare-partial-views"></a>Déclarer des vues partielles

::: moniker range=">= aspnetcore-2.0"

Une vue partielle est un fichier de balisage *. cshtml* sans [`@page`](xref:mvc/views/razor#page) directive gérée dans le dossier *views* (MVC) ou le dossier *pages* ( Razor pages).

Dans ASP.NET Core MVC, le <xref:Microsoft.AspNetCore.Mvc.ViewResult> d’un contrôleur peut retourner une vue ou une vue partielle. Dans Razor les pages, un <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> peut retourner une vue partielle représentée comme un <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> objet. Le référencement et le rendu des vues partielles sont décrits dans la section [Référencer une vue partielle](#reference-a-partial-view).

Contrairement au rendu d’une vue MVC ou d’une page, une vue partielle n’exécute pas *_ViewStart.cshtml*. Pour plus d’informations sur *_ViewStart.cshtml*, consultez <xref:mvc/views/layout>.

Les noms de fichiers des vues partielles commencent souvent par un trait de soulignement (`_`). Cette convention de nommage n’est pas obligatoire, mais elle aide à différencier visuellement les vues partielles des autres vues et pages.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Une vue partielle est un fichier de balisage *.cshtml* tenu à jour dans le dossier *Vues*.

Le <xref:Microsoft.AspNetCore.Mvc.ViewResult> d’un contrôleur peut retourner une vue ou une vue partielle. Le référencement et le rendu des vues partielles sont décrits dans la section [Référencer une vue partielle](#reference-a-partial-view).

Contrairement au rendu d’une vue MVC, une vue partielle n’exécute pas *_ViewStart.cshtml*. Pour plus d’informations sur *_ViewStart.cshtml*, consultez <xref:mvc/views/layout>.

Les noms de fichiers des vues partielles commencent souvent par un trait de soulignement (`_`). Cette convention de nommage n’est pas obligatoire, mais elle aide à différencier visuellement les vues partielles des autres vues.

::: moniker-end

## <a name="reference-a-partial-view"></a>Référencer une vue partielle

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-no-locrazor-pages-pagemodel"></a>Utiliser une vue partielle dans une Razor page PageModel

Dans ASP.NET Core 2,0 ou 2,1, la méthode de gestionnaire suivante restitue la vue partielle *\_ AuthorPartialRP. cshtml* à la réponse :

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Avec ASP.NET Core 2.2 ou version ultérieure, une méthode de gestionnaire peut également appeler la méthode <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> pour générer un objet `PartialViewResult` :

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a>Utiliser une vue partielle dans un fichier de balises

::: moniker range=">= aspnetcore-2.1"

Il y a plusieurs façons de référencer une vue partielle au sein d’un fichier de balisage. Pour vos applications, nous vous recommandons de choisir l’une des approches de rendu asynchrone suivantes :

* [Tag Helper Partial](#partial-tag-helper)
* [Assistance HTML asynchrone](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

Il y a deux façons de référencer une vue partielle au sein d’un fichier de balisage :

* [Assistance HTML asynchrone](#asynchronous-html-helper)
* [Assistance HTML synchrone](#synchronous-html-helper)

Pour vos applications, nous vous recommandons d’utiliser le [Helper HTML asynchrone](#asynchronous-html-helper).

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a>Tag Helper Partial

Le [tag Helper partiel](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requiert ASP.net Core 2,1 ou une version ultérieure.

Le Tag Helper Partial effectue un rendu asynchrone et utilise une syntaxe de type HTML :

```cshtml
<partial name="_PartialName" />
```

Si une extension de fichier est spécifiée, la vue partielle référencée par le Tag Helper doit se trouver dans le même dossier que le fichier de balisage qui appelle la vue partielle :

```cshtml
<partial name="_PartialName.cshtml" />
```

L’exemple suivant référence une vue partielle à la racine de l’application. Les chemins qui commencent par un tilde et une barre oblique (`~/`) ou par une barre oblique seule (`/`) renvoient à la racine de l’application :

**Razor Pages**

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

**MVC**

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

L’exemple suivant référence une vue partielle avec un chemin relatif :

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

Pour plus d'informations, consultez <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.

::: moniker-end

### <a name="asynchronous-html-helper"></a>Assistance HTML asynchrone

Avec un Helper HTML, la bonne pratique est d’utiliser <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>. `PartialAsync` retourne un type <xref:Microsoft.AspNetCore.Html.IHtmlContent> wrappé dans un <xref:System.Threading.Tasks.Task%601>. La méthode est référencée par l’ajout du préfixe `@` à l’appel attendu :

```cshtml
@await Html.PartialAsync("_PartialName")
```

Si l’extension de fichier est spécifiée, la vue partielle référencée par le Helper HTML doit se trouver dans le même dossier que le fichier de balisage qui appelle la vue partielle :

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

L’exemple suivant référence une vue partielle à la racine de l’application. Les chemins qui commencent par un tilde et une barre oblique (`~/`) ou par une barre oblique seule (`/`) renvoient à la racine de l’application :

::: moniker range=">= aspnetcore-2.1"

**Razor Pages**

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

**MVC**

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

L’exemple suivant référence une vue partielle avec un chemin relatif :

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

Vous pouvez aussi effectuer le rendu d’une vue partielle avec <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>. Cette méthode ne retourne aucun <xref:Microsoft.AspNetCore.Html.IHtmlContent>. Elle envoie la sortie rendue directement à la réponse. Étant donné que la méthode ne retourne pas de résultat, elle doit être appelée dans un Razor bloc de code :

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

Dans la mesure où elle envoie directement le contenu rendu, la méthode `RenderPartialAsync` est plus efficace dans certains scénarios. Dans les scénarios nécessitant de hautes performances, effectuez un test d’évaluation de la page à l’aide de ces deux approches et choisissez celle qui génère une réponse plus rapidement.

### <a name="synchronous-html-helper"></a>Assistance HTML synchrone

<xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> et <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> sont les équivalents synchrones de `PartialAsync` et `RenderPartialAsync`, respectivement. Les équivalents synchrones ne sont pas recommandés en raison du risque d’interblocage dans certains scénarios. Les méthodes synchrones sont prévues d’être supprimées dans une version ultérieure.

> [!IMPORTANT]
> Si vous devez exécuter du code, utilisez un [composant de vue](xref:mvc/views/view-components) au lieu d’une vue partielle.

::: moniker range=">= aspnetcore-2.1"

L’appel de `Partial` ou `RenderPartial` génère un avertissement de l’analyseur Visual Studio. Par exemple, la présence de `Partial` génère le message d’avertissement suivant :

> L’utilisation de IHtmlHelper.Partial peut entraîner des interblocages d’application. Utilisez plutôt un Tag Helper &lt;Partial&gt; ou IHtmlHelper.PartialAsync.

Remplacez les appels à `@Html.Partial` avec `@await Html.PartialAsync` ou le [tag Helper partiel](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper). Pour plus d’informations sur la migration du Tag Helper Partial, consultez [Migrer à partir d’une assistance HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).

::: moniker-end

## <a name="partial-view-discovery"></a>Découverte des vues partielles

Quand une vue partielle est référencée par son nom sans extension de fichier, les emplacements suivants sont recherchés dans l’ordre indiqué :

::: moniker range=">= aspnetcore-2.1"

**Razor Pages**

1. Dossier de la page en cours d’exécution
1. Directory Graph au-dessus du dossier de la page
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

**MVC**

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

Les conventions suivantes s’appliquent à la détection des vues partielles :

* Vous pouvez avoir plusieurs vues partielles avec le même nom de fichier à condition que les vues partielles se trouvent dans des dossiers distincts.
* Quand vous référencez une vue partielle par son nom (sans extension de fichier) et que la vue partielle est présente à la fois dans le dossier de l’appelant et le dossier *Partagé*, la vue partielle fournie est celle du dossier de l’appelant. Si la vue partielle n’est pas présente dans le dossier de l’appelant, la vue partielle fournie est celle du dossier *Partagé*. Les vues partielles dans le dossier *Partagé* sont appelées *vues partielles partagées* ou *vues partielles par défaut*.
* Les vues partielles peuvent être *chaînées* &mdash; une vue partielle peut appeler une autre vue partielle si une référence circulaire n’est pas formée par les appels. Les chemins relatifs sont toujours relatifs au fichier actuel, et non au fichier racine ou parent associé.

> [!NOTE]
> Un [Razor](xref:mvc/views/razor) `section` défini dans une vue partielle est invisible aux fichiers de balisage parents. La `section` est visible uniquement par la vue partielle dans laquelle elle est définie.

## <a name="access-data-from-partial-views"></a>Accéder à des données à partir de vues partielles

Quand une vue partielle est instanciée, elle reçoit une *copie* du dictionnaire `ViewData` du parent. Les mises à jour apportées aux données de la vue partielle ne sont pas conservées dans la vue parente. Tout changement apporté à `ViewData` dans une vue partielle est perdu quand la vue partielle est retournée.

L’exemple suivant montre comment passer une instance de [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) à une vue partielle :

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

Vous pouvez passer un modèle dans une vue partielle. Le modèle peut être un objet personnalisé. Vous pouvez passer un modèle avec `PartialAsync` (envoie le rendu d’un bloc de contenu à l’appelant) ou avec `RenderPartialAsync` (transmet le contenu à la sortie) :

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

**Razor Pages**

Le balisage suivant dans l’exemple d’application est extrait de la page *Pages/ArticlesRP/ReadRP.cshtml*. La page contient deux vues partielles. La seconde vue partielle passe un modèle et `ViewData` à la vue partielle. La surcharge de constructeur `ViewDataDictionary` passe un nouveau dictionnaire `ViewData` tout en conservant le dictionnaire `ViewData` existant.

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

*Pages/Shared/_AuthorPartialRP.cshtml* est la première vue partielle référencée par le fichier de balisage *ReadRP.cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

*Pages/ArticlesRP/_ArticleSectionRP.cshtml* est la seconde vue partielle référencée par le fichier de balisage *ReadRP.cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

**MVC**

::: moniker-end

Le balisage suivant dans l’exemple d’application illustre la vue *Views/Articles/Read.cshtml*. La vue contient deux vues partielles. La seconde vue partielle passe un modèle et `ViewData` à la vue partielle. La surcharge de constructeur `ViewDataDictionary` passe un nouveau dictionnaire `ViewData` tout en conservant le dictionnaire `ViewData` existant.

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

*Views/Shared/_AuthorPartial. cshtml* est la première vue partielle référencée par le fichier de balisage *Read. cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

*Views/Articles/_ArticleSection.cshtml* est la seconde vue partielle référencée par le fichier de balisage *Read.cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

Au moment de l’exécution, le rendu des vues partielles est effectué dans la sortie rendue du fichier de balisage parent, qui est elle-même rendue dans le fichier *_Layout.cshtml* partagé. La première vue partielle affiche le nom de l’auteur et la date de publication de l’article :

> Abraham Lincoln
>
> This partial view from &lt;shared partial view file path&gt;.
> 11/19/1863 12:00:00 AM

La seconde vue partielle affiche les sections de l’article :

> Section One Index: 0
>
> Four score and seven years ago ...
>
> Section Two Index: 1
>
> Now we are engaged in a great civil war, testing ...
>
> Section Three Index: 2
>
> But, in a larger sense, we can not dedicate ...

## <a name="additional-resources"></a>Ressources supplémentaires

::: moniker range=">= aspnetcore-2.1"

* [Razor Référence de syntaxe pour ASP.NET Core](xref:mvc/views/razor)
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* [Razor Référence de syntaxe pour ASP.NET Core](xref:mvc/views/razor)
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
