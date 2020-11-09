---
title: Vues partielles dans ASP.NET Core
author: ardalis
description: Découvrez comment utiliser les vues partielles pour découper des fichiers de balisage volumineux et réduire la duplication du balisage commun entre les différentes pages web dans les applications ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
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
uid: mvc/views/partial
ms.openlocfilehash: 01fb87205f7855f0995cbcd135b6b01f15835e3b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060597"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="8e0f9-103">Vues partielles dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e0f9-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="8e0f9-104">Par [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT) et [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="8e0f9-104">By [Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="8e0f9-105">Une vue partielle est un [Razor](xref:mvc/views/razor) fichier de balisage ( *. cshtml* ) sans [`@page`](xref:mvc/views/razor#page) directive qui restitue la sortie HTML *dans* la sortie rendue d’un autre fichier de balisage.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-105">A partial view is a [Razor](xref:mvc/views/razor) markup file ( *.cshtml* ) without an [`@page`](xref:mvc/views/razor#page) directive that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8e0f9-106">Le terme *vue partielle* est utilisé lors du développement d’une application MVC, où les fichiers de balisage sont appelés *vues* ou Razor application pages, où les fichiers de balisage sont appelés *pages* .</span><span class="sxs-lookup"><span data-stu-id="8e0f9-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views* , or a Razor Pages app, where markup files are called *pages* .</span></span> <span data-ttu-id="8e0f9-107">Cette rubrique fait référence aux pages vues et Razor pages MVC en tant que *fichiers de balisage* .</span><span class="sxs-lookup"><span data-stu-id="8e0f9-107">This topic generically refers to MVC views and Razor Pages pages as *markup files* .</span></span>

::: moniker-end

<span data-ttu-id="8e0f9-108">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8e0f9-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="8e0f9-109">Quand utiliser des vues partielles ?</span><span class="sxs-lookup"><span data-stu-id="8e0f9-109">When to use partial views</span></span>

<span data-ttu-id="8e0f9-110">Les vues partielles sont utiles pour :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="8e0f9-111">Découper des fichiers de balisage volumineux en composants plus petits.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="8e0f9-112">Dans un grand fichier de balisage complexe composé de plusieurs parties logiques, il peut s’avérer utile de travailler sur chaque partie isolée dans une vue partielle.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="8e0f9-113">Le code dans le fichier de balisage est facile à gérer, puisqu’il contient uniquement la structure de page globale et les références aux vues partielles.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="8e0f9-114">Réduire la duplication du contenu de balisage commun entre les fichiers de balisage.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="8e0f9-115">Quand les mêmes éléments de balisage sont utilisés entre plusieurs fichiers de balisage, une vue partielle supprime la duplication du contenu de balisage dans un seul fichier de vue partielle.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="8e0f9-116">Quand le balisage est modifié dans la vue partielle, il met à jour la sortie rendue des fichiers de balisage qui utilisent cette vue partielle.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="8e0f9-117">Les vues partielles ne doivent pas être utilisées pour tenir à jour des éléments de disposition communs.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="8e0f9-118">Ces éléments doivent être spécifiés dans des fichiers [_Layout.cshtml](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="8e0f9-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="8e0f9-119">N’utilisez pas une vue partielle quand du code ou une logique de rendu complexe doit être exécuté pour effectuer le rendu du balisage.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="8e0f9-120">Dans ce cas, utilisez un [composant de vue](xref:mvc/views/view-components) à la place.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="8e0f9-121">Déclarer des vues partielles</span><span class="sxs-lookup"><span data-stu-id="8e0f9-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8e0f9-122">Une vue partielle est un fichier de balisage *. cshtml* sans [`@page`](xref:mvc/views/razor#page) directive gérée dans le dossier *views* (MVC) ou le dossier *pages* ( Razor pages).</span><span class="sxs-lookup"><span data-stu-id="8e0f9-122">A partial view is a *.cshtml* markup file without an [`@page`](xref:mvc/views/razor#page) directive maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="8e0f9-123">Dans ASP.NET Core MVC, le <xref:Microsoft.AspNetCore.Mvc.ViewResult> d’un contrôleur peut retourner une vue ou une vue partielle.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="8e0f9-124">Dans Razor les pages, un <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> peut retourner une vue partielle représentée comme un <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> objet.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-124">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object.</span></span> <span data-ttu-id="8e0f9-125">Le référencement et le rendu des vues partielles sont décrits dans la section [Référencer une vue partielle](#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="8e0f9-125">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="8e0f9-126">Contrairement au rendu d’une vue MVC ou d’une page, une vue partielle n’exécute pas *_ViewStart.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="8e0f9-126">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml* .</span></span> <span data-ttu-id="8e0f9-127">Pour plus d’informations sur *_ViewStart.cshtml* , consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-127">For more information on *_ViewStart.cshtml* , see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="8e0f9-128">Les noms de fichiers des vues partielles commencent souvent par un trait de soulignement (`_`).</span><span class="sxs-lookup"><span data-stu-id="8e0f9-128">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="8e0f9-129">Cette convention de nommage n’est pas obligatoire, mais elle aide à différencier visuellement les vues partielles des autres vues et pages.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-129">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="8e0f9-130">Une vue partielle est un fichier de balisage *.cshtml* tenu à jour dans le dossier *Vues* .</span><span class="sxs-lookup"><span data-stu-id="8e0f9-130">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="8e0f9-131">Le <xref:Microsoft.AspNetCore.Mvc.ViewResult> d’un contrôleur peut retourner une vue ou une vue partielle.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-131">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="8e0f9-132">Le référencement et le rendu des vues partielles sont décrits dans la section [Référencer une vue partielle](#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="8e0f9-132">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="8e0f9-133">Contrairement au rendu d’une vue MVC, une vue partielle n’exécute pas *_ViewStart.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="8e0f9-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml* .</span></span> <span data-ttu-id="8e0f9-134">Pour plus d’informations sur *_ViewStart.cshtml* , consultez <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-134">For more information on *_ViewStart.cshtml* , see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="8e0f9-135">Les noms de fichiers des vues partielles commencent souvent par un trait de soulignement (`_`).</span><span class="sxs-lookup"><span data-stu-id="8e0f9-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="8e0f9-136">Cette convention de nommage n’est pas obligatoire, mais elle aide à différencier visuellement les vues partielles des autres vues.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="8e0f9-137">Référencer une vue partielle</span><span class="sxs-lookup"><span data-stu-id="8e0f9-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-no-locrazor-pages-pagemodel"></a><span data-ttu-id="8e0f9-138">Utiliser une vue partielle dans une Razor page PageModel</span><span class="sxs-lookup"><span data-stu-id="8e0f9-138">Use a partial view in a Razor Pages PageModel</span></span>

<span data-ttu-id="8e0f9-139">Dans ASP.NET Core 2,0 ou 2,1, la méthode de gestionnaire suivante restitue la vue partielle *\_ AuthorPartialRP. cshtml* à la réponse :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-139">In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:</span></span>

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

<span data-ttu-id="8e0f9-140">Avec ASP.NET Core 2.2 ou version ultérieure, une méthode de gestionnaire peut également appeler la méthode <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> pour générer un objet `PartialViewResult` :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-140">In ASP.NET Core 2.2 or later, a handler method can alternatively call the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> method to produce a `PartialViewResult` object:</span></span>

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a><span data-ttu-id="8e0f9-141">Utiliser une vue partielle dans un fichier de balises</span><span class="sxs-lookup"><span data-stu-id="8e0f9-141">Use a partial view in a markup file</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8e0f9-142">Il y a plusieurs façons de référencer une vue partielle au sein d’un fichier de balisage.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-142">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="8e0f9-143">Pour vos applications, nous vous recommandons de choisir l’une des approches de rendu asynchrone suivantes :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-143">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="8e0f9-144">Tag Helper Ppartial</span><span class="sxs-lookup"><span data-stu-id="8e0f9-144">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="8e0f9-145">Assistance HTML asynchrone</span><span class="sxs-lookup"><span data-stu-id="8e0f9-145">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="8e0f9-146">Il y a deux façons de référencer une vue partielle au sein d’un fichier de balisage :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-146">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="8e0f9-147">Assistance HTML asynchrone</span><span class="sxs-lookup"><span data-stu-id="8e0f9-147">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="8e0f9-148">Assistance HTML synchrone</span><span class="sxs-lookup"><span data-stu-id="8e0f9-148">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="8e0f9-149">Pour vos applications, nous vous recommandons d’utiliser le [Helper HTML asynchrone](#asynchronous-html-helper).</span><span class="sxs-lookup"><span data-stu-id="8e0f9-149">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="8e0f9-150">Tag Helper Partial</span><span class="sxs-lookup"><span data-stu-id="8e0f9-150">Partial Tag Helper</span></span>

<span data-ttu-id="8e0f9-151">Le [tag Helper partiel](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requiert ASP.net Core 2,1 ou une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-151">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="8e0f9-152">Le Tag Helper Partial effectue un rendu asynchrone et utilise une syntaxe de type HTML :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-152">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="8e0f9-153">Si une extension de fichier est spécifiée, la vue partielle référencée par le Tag Helper doit se trouver dans le même dossier que le fichier de balisage qui appelle la vue partielle :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-153">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="8e0f9-154">L’exemple suivant référence une vue partielle à la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-154">The following example references a partial view from the app root.</span></span> <span data-ttu-id="8e0f9-155">Les chemins qui commencent par un tilde et une barre oblique (`~/`) ou par une barre oblique seule (`/`) renvoient à la racine de l’application :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-155">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="8e0f9-156">**Razor Pages**</span><span class="sxs-lookup"><span data-stu-id="8e0f9-156">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="8e0f9-157">**MVC**</span><span class="sxs-lookup"><span data-stu-id="8e0f9-157">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="8e0f9-158">L’exemple suivant référence une vue partielle avec un chemin relatif :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-158">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="8e0f9-159">Pour plus d'informations, consultez <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-159">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="8e0f9-160">Assistance HTML asynchrone</span><span class="sxs-lookup"><span data-stu-id="8e0f9-160">Asynchronous HTML Helper</span></span>

<span data-ttu-id="8e0f9-161">Avec un Helper HTML, la bonne pratique est d’utiliser <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-161">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="8e0f9-162">`PartialAsync` retourne un type <xref:Microsoft.AspNetCore.Html.IHtmlContent> wrappé dans un <xref:System.Threading.Tasks.Task%601>.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-162">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="8e0f9-163">La méthode est référencée par l’ajout du préfixe `@` à l’appel attendu :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-163">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="8e0f9-164">Si l’extension de fichier est spécifiée, la vue partielle référencée par le Helper HTML doit se trouver dans le même dossier que le fichier de balisage qui appelle la vue partielle :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-164">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="8e0f9-165">L’exemple suivant référence une vue partielle à la racine de l’application.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-165">The following example references a partial view from the app root.</span></span> <span data-ttu-id="8e0f9-166">Les chemins qui commencent par un tilde et une barre oblique (`~/`) ou par une barre oblique seule (`/`) renvoient à la racine de l’application :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-166">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8e0f9-167">**Razor Pages**</span><span class="sxs-lookup"><span data-stu-id="8e0f9-167">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="8e0f9-168">**MVC**</span><span class="sxs-lookup"><span data-stu-id="8e0f9-168">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="8e0f9-169">L’exemple suivant référence une vue partielle avec un chemin relatif :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-169">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="8e0f9-170">Vous pouvez aussi effectuer le rendu d’une vue partielle avec <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-170">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="8e0f9-171">Cette méthode ne retourne aucun <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-171">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="8e0f9-172">Elle envoie la sortie rendue directement à la réponse.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-172">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="8e0f9-173">Étant donné que la méthode ne retourne pas de résultat, elle doit être appelée dans un Razor bloc de code :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-173">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="8e0f9-174">Dans la mesure où elle envoie directement le contenu rendu, la méthode `RenderPartialAsync` est plus efficace dans certains scénarios.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-174">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="8e0f9-175">Dans les scénarios nécessitant de hautes performances, effectuez un test d’évaluation de la page à l’aide de ces deux approches et choisissez celle qui génère une réponse plus rapidement.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-175">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="8e0f9-176">Assistance HTML synchrone</span><span class="sxs-lookup"><span data-stu-id="8e0f9-176">Synchronous HTML Helper</span></span>

<span data-ttu-id="8e0f9-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> et <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> sont les équivalents synchrones de `PartialAsync` et `RenderPartialAsync`, respectivement.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-177"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="8e0f9-178">Les équivalents synchrones ne sont pas recommandés en raison du risque d’interblocage dans certains scénarios.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-178">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="8e0f9-179">Les méthodes synchrones sont prévues d’être supprimées dans une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-179">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8e0f9-180">Si vous devez exécuter du code, utilisez un [composant de vue](xref:mvc/views/view-components) au lieu d’une vue partielle.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-180">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8e0f9-181">L’appel de `Partial` ou `RenderPartial` génère un avertissement de l’analyseur Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-181">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="8e0f9-182">Par exemple, la présence de `Partial` génère le message d’avertissement suivant :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-182">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="8e0f9-183">L’utilisation de IHtmlHelper.Partial peut entraîner des interblocages d’application.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-183">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="8e0f9-184">Utilisez plutôt un Tag Helper &lt;Partial&gt; ou IHtmlHelper.PartialAsync.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-184">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="8e0f9-185">Remplacez les appels à `@Html.Partial` avec `@await Html.PartialAsync` ou le [tag Helper partiel](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="8e0f9-185">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="8e0f9-186">Pour plus d’informations sur la migration du Tag Helper Partial, consultez [Migrer à partir d’une assistance HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span><span class="sxs-lookup"><span data-stu-id="8e0f9-186">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="8e0f9-187">Découverte des vues partielles</span><span class="sxs-lookup"><span data-stu-id="8e0f9-187">Partial view discovery</span></span>

<span data-ttu-id="8e0f9-188">Quand une vue partielle est référencée par son nom sans extension de fichier, les emplacements suivants sont recherchés dans l’ordre indiqué :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-188">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8e0f9-189">**Razor Pages**</span><span class="sxs-lookup"><span data-stu-id="8e0f9-189">**Razor Pages**</span></span>

1. <span data-ttu-id="8e0f9-190">Dossier de la page en cours d’exécution</span><span class="sxs-lookup"><span data-stu-id="8e0f9-190">Currently executing page's folder</span></span>
1. <span data-ttu-id="8e0f9-191">Directory Graph au-dessus du dossier de la page</span><span class="sxs-lookup"><span data-stu-id="8e0f9-191">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="8e0f9-192">**MVC**</span><span class="sxs-lookup"><span data-stu-id="8e0f9-192">**MVC**</span></span>

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

<span data-ttu-id="8e0f9-193">Les conventions suivantes s’appliquent à la détection des vues partielles :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-193">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="8e0f9-194">Vous pouvez avoir plusieurs vues partielles avec le même nom de fichier à condition que les vues partielles se trouvent dans des dossiers distincts.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-194">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="8e0f9-195">Quand vous référencez une vue partielle par son nom (sans extension de fichier) et que la vue partielle est présente à la fois dans le dossier de l’appelant et le dossier *Partagé* , la vue partielle fournie est celle du dossier de l’appelant.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-195">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="8e0f9-196">Si la vue partielle n’est pas présente dans le dossier de l’appelant, la vue partielle fournie est celle du dossier *Partagé* .</span><span class="sxs-lookup"><span data-stu-id="8e0f9-196">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="8e0f9-197">Les vues partielles dans le dossier *Partagé* sont appelées *vues partielles partagées* ou *vues partielles par défaut* .</span><span class="sxs-lookup"><span data-stu-id="8e0f9-197">Partial views in the *Shared* folder are called *shared partial views* or *default partial views* .</span></span>
* <span data-ttu-id="8e0f9-198">Les vues partielles peuvent être *chaînées* &mdash; une vue partielle peut appeler une autre vue partielle si une référence circulaire n’est pas formée par les appels.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-198">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="8e0f9-199">Les chemins relatifs sont toujours relatifs au fichier actuel, et non au fichier racine ou parent associé.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-199">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="8e0f9-200">Un [Razor](xref:mvc/views/razor) `section` défini dans une vue partielle est invisible aux fichiers de balisage parents.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-200">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="8e0f9-201">La `section` est visible uniquement par la vue partielle dans laquelle elle est définie.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-201">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="8e0f9-202">Accéder à des données à partir de vues partielles</span><span class="sxs-lookup"><span data-stu-id="8e0f9-202">Access data from partial views</span></span>

<span data-ttu-id="8e0f9-203">Quand une vue partielle est instanciée, elle reçoit une *copie* du dictionnaire `ViewData` du parent.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-203">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="8e0f9-204">Les mises à jour apportées aux données de la vue partielle ne sont pas conservées dans la vue parente.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-204">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="8e0f9-205">Tout changement apporté à `ViewData` dans une vue partielle est perdu quand la vue partielle est retournée.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-205">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="8e0f9-206">L’exemple suivant montre comment passer une instance de [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) à une vue partielle :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-206">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="8e0f9-207">Vous pouvez passer un modèle dans une vue partielle.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-207">You can pass a model into a partial view.</span></span> <span data-ttu-id="8e0f9-208">Le modèle peut être un objet personnalisé.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-208">The model can be a custom object.</span></span> <span data-ttu-id="8e0f9-209">Vous pouvez passer un modèle avec `PartialAsync` (envoie le rendu d’un bloc de contenu à l’appelant) ou avec `RenderPartialAsync` (transmet le contenu à la sortie) :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-209">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8e0f9-210">**Razor Pages**</span><span class="sxs-lookup"><span data-stu-id="8e0f9-210">**Razor Pages**</span></span>

<span data-ttu-id="8e0f9-211">Le balisage suivant dans l’exemple d’application est extrait de la page *Pages/ArticlesRP/ReadRP.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="8e0f9-211">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="8e0f9-212">La page contient deux vues partielles.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-212">The page contains two partial views.</span></span> <span data-ttu-id="8e0f9-213">La seconde vue partielle passe un modèle et `ViewData` à la vue partielle.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-213">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="8e0f9-214">La surcharge de constructeur `ViewDataDictionary` passe un nouveau dictionnaire `ViewData` tout en conservant le dictionnaire `ViewData` existant.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-214">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

<span data-ttu-id="8e0f9-215">*Pages/Shared/_AuthorPartialRP.cshtml* est la première vue partielle référencée par le fichier de balisage *ReadRP.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-215">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="8e0f9-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* est la seconde vue partielle référencée par le fichier de balisage *ReadRP.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-216">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="8e0f9-217">**MVC**</span><span class="sxs-lookup"><span data-stu-id="8e0f9-217">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="8e0f9-218">Le balisage suivant dans l’exemple d’application illustre la vue *Views/Articles/Read.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="8e0f9-218">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="8e0f9-219">La vue contient deux vues partielles.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-219">The view contains two partial views.</span></span> <span data-ttu-id="8e0f9-220">La seconde vue partielle passe un modèle et `ViewData` à la vue partielle.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-220">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="8e0f9-221">La surcharge de constructeur `ViewDataDictionary` passe un nouveau dictionnaire `ViewData` tout en conservant le dictionnaire `ViewData` existant.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-221">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

<span data-ttu-id="8e0f9-222">*Views/Shared/_AuthorPartial. cshtml* est la première vue partielle référencée par le fichier de balisage *Read. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-222">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="8e0f9-223">*Views/Articles/_ArticleSection.cshtml* est la seconde vue partielle référencée par le fichier de balisage *Read.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-223">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="8e0f9-224">Au moment de l’exécution, le rendu des vues partielles est effectué dans la sortie rendue du fichier de balisage parent, qui est elle-même rendue dans le fichier *_Layout.cshtml* partagé.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-224">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml* .</span></span> <span data-ttu-id="8e0f9-225">La première vue partielle affiche le nom de l’auteur et la date de publication de l’article :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-225">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="8e0f9-226">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="8e0f9-226">Abraham Lincoln</span></span>
>
> <span data-ttu-id="8e0f9-227">This partial view from &lt;shared partial view file path&gt;.</span><span class="sxs-lookup"><span data-stu-id="8e0f9-227">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="8e0f9-228">11/19/1863 12:00:00 AM</span><span class="sxs-lookup"><span data-stu-id="8e0f9-228">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="8e0f9-229">La seconde vue partielle affiche les sections de l’article :</span><span class="sxs-lookup"><span data-stu-id="8e0f9-229">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="8e0f9-230">Section One Index: 0</span><span class="sxs-lookup"><span data-stu-id="8e0f9-230">Section One Index: 0</span></span>
>
> <span data-ttu-id="8e0f9-231">Four score and seven years ago ...</span><span class="sxs-lookup"><span data-stu-id="8e0f9-231">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="8e0f9-232">Section Two Index: 1</span><span class="sxs-lookup"><span data-stu-id="8e0f9-232">Section Two Index: 1</span></span>
>
> <span data-ttu-id="8e0f9-233">Now we are engaged in a great civil war, testing ...</span><span class="sxs-lookup"><span data-stu-id="8e0f9-233">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="8e0f9-234">Section Three Index: 2</span><span class="sxs-lookup"><span data-stu-id="8e0f9-234">Section Three Index: 2</span></span>
>
> <span data-ttu-id="8e0f9-235">But, in a larger sense, we can not dedicate ...</span><span class="sxs-lookup"><span data-stu-id="8e0f9-235">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e0f9-236">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="8e0f9-236">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
