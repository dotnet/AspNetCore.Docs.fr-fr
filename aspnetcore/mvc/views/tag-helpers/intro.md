---
title: Tag Helpers dans ASP.NET Core
author: rick-anderson
description: Découvrez ce que sont les Tag Helpers et comment les utiliser dans ASP.NET Core.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
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
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 781365d99c6d36d8abaec9681128ba712db8cb88
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060662"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="c0699-103">Tag Helpers dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0699-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="c0699-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0699-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="c0699-105">Que sont les Tag Helpers ?</span><span class="sxs-lookup"><span data-stu-id="c0699-105">What are Tag Helpers</span></span>

<span data-ttu-id="c0699-106">Les tag helpers permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers.</span><span class="sxs-lookup"><span data-stu-id="c0699-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="c0699-107">Par exemple, le `ImageTagHelper` intégré peut ajouter un numéro de version au nom de l’image.</span><span class="sxs-lookup"><span data-stu-id="c0699-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="c0699-108">Chaque fois que l’image change, le serveur en génère une nouvelle version unique, pour que les clients soient sûrs d’obtenir l’image actuelle (au lieu d’une image mise en cache obsolète).</span><span class="sxs-lookup"><span data-stu-id="c0699-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="c0699-109">Il existe de nombreux Tag Helpers pour les tâches courantes (par exemple la création de formulaires ou de liens, le chargement de ressources, etc.) et bien d’autres encore, dans les dépôts GitHub publics et sous forme de packages NuGet.</span><span class="sxs-lookup"><span data-stu-id="c0699-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="c0699-110">Les Tag helpers sont créés en c#, et ils ciblent des éléments HTML en fonction de la balise parente, du nom d’attribut ou du nom de l’élément.</span><span class="sxs-lookup"><span data-stu-id="c0699-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="c0699-111">Par exemple, le `LabelTagHelper` intégré peut cibler l’élément `<label>` HTML quand les attributs `LabelTagHelper` sont appliqués.</span><span class="sxs-lookup"><span data-stu-id="c0699-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="c0699-112">Si vous êtes familiarisé avec les [applications auxiliaires html](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), les balises helpers réduisent les transitions explicites entre html et C# dans les Razor vues.</span><span class="sxs-lookup"><span data-stu-id="c0699-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in Razor views.</span></span> <span data-ttu-id="c0699-113">Dans de nombreux cas, les HTML Helpers offrent une autre approche par rapport à un Tag Helper spécifique. Toutefois, il est clair que les Tag Helpers ne remplacent pas les HTML Helpers, et qu’il n’existe pas toujours un Tag Helper pour chaque HTML Helper.</span><span class="sxs-lookup"><span data-stu-id="c0699-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="c0699-114">[Comparaison des Tag Helpers aux HTML Helpers](#tag-helpers-compared-to-html-helpers) explique les différences de façon plus approfondie.</span><span class="sxs-lookup"><span data-stu-id="c0699-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="c0699-115">Ce que fournissent des Tag helpers</span><span class="sxs-lookup"><span data-stu-id="c0699-115">What Tag Helpers provide</span></span>

<span data-ttu-id="c0699-116">**Une expérience de développement HTML conviviale** Dans la plupart des cas, le Razor balisage à l’aide de tag Helper ressemble à du code HTML standard.</span><span class="sxs-lookup"><span data-stu-id="c0699-116">**An HTML-friendly development experience** For the most part, Razor markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="c0699-117">Les concepteurs frontaux conversions avec HTML/CSS/JavaScript peuvent être modifiés Razor sans apprentissage de la Razor syntaxe C#.</span><span class="sxs-lookup"><span data-stu-id="c0699-117">Front-end designers conversant with HTML/CSS/JavaScript can edit Razor without learning C# Razor syntax.</span></span>

<span data-ttu-id="c0699-118">**Un environnement IntelliSense élaboré pour la création de code HTML et de Razor balisage est un** contraste aigu avec les applications auxiliaires html, l’approche précédente de la création côté serveur de balises dans les Razor vues.</span><span class="sxs-lookup"><span data-stu-id="c0699-118">**A rich IntelliSense environment for creating HTML and Razor markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in Razor views.</span></span> <span data-ttu-id="c0699-119">[Comparaison des Tag Helpers aux HTML Helpers](#tag-helpers-compared-to-html-helpers) explique les différences de façon plus approfondie.</span><span class="sxs-lookup"><span data-stu-id="c0699-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="c0699-120">[Prise en charge IntelliSense des Tag Helpers](#intellisense-support-for-tag-helpers) explique l’environnement IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="c0699-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="c0699-121">Même les développeurs expérimentés avec la Razor syntaxe c# sont plus productifs en utilisant des balises tag que l’écriture d’un Razor balisage c#.</span><span class="sxs-lookup"><span data-stu-id="c0699-121">Even developers experienced with Razor C# syntax are more productive using Tag Helpers than writing C# Razor markup.</span></span>

<span data-ttu-id="c0699-122">**Un moyen d’améliorer votre productivité et votre capacité à produire du code plus robuste, fiable et facile à gérer en utilisant des informations uniquement disponibles sur le serveur** Par exemple, l’ancien usage pour la mise à jour des images consistait à modifier le nom de l’image quand vous modifiiez l’image.</span><span class="sxs-lookup"><span data-stu-id="c0699-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="c0699-123">Les images doivent être efficacement mises en cache pour des raisons de performance, et à moins de modifier le nom d’une image, les clients risquent d’obtenir une copie obsolète.</span><span class="sxs-lookup"><span data-stu-id="c0699-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="c0699-124">Auparavant, une fois qu’une image avait été modifiée, le nom devait être modifié et chaque référence à l’image dans l’application web avait besoin d’être mise à jour.</span><span class="sxs-lookup"><span data-stu-id="c0699-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="c0699-125">Non seulement cela nécessite beaucoup de travail, mais elle est également sujette aux erreurs (vous pourriez manquer une référence, entrer accidentellement une chaîne incorrecte, etc.). Le intégré peut le `ImageTagHelper` faire automatiquement pour vous.</span><span class="sxs-lookup"><span data-stu-id="c0699-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="c0699-126">Le `ImageTagHelper` peut ajouter un numéro de version au nom de l’image, si bien que dès que l’image change, le serveur génère automatiquement une nouvelle version unique de l’image.</span><span class="sxs-lookup"><span data-stu-id="c0699-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="c0699-127">Les clients sont sûrs d’obtenir l’image actuelle.</span><span class="sxs-lookup"><span data-stu-id="c0699-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="c0699-128">Cette robustesse et cette économie de travail sont inhérentes à l’utilisation du `ImageTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="c0699-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="c0699-129">La plupart des Tag Helpers intégrés ciblent les éléments HTML standard et fournissent des attributs côté serveur pour l’élément.</span><span class="sxs-lookup"><span data-stu-id="c0699-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="c0699-130">Par exemple, l’élément `<input>` utilisé dans de nombreuses vues dans le dossier *Views/Account* contient l’attribut `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="c0699-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="c0699-131">Cet attribut extrait le nom de la propriété de modèle spécifiée dans le code HTML restitué.</span><span class="sxs-lookup"><span data-stu-id="c0699-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="c0699-132">Prenons Razor l’exemple d’une vue avec le modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="c0699-132">Consider a Razor view with the following model:</span></span>

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

<span data-ttu-id="c0699-133">Le Razor balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="c0699-133">The following Razor markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="c0699-134">Génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="c0699-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="c0699-135">L’attribut `asp-for` est rendu disponible par la propriété `For` dans le [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span><span class="sxs-lookup"><span data-stu-id="c0699-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="c0699-136">Pour plus d’informations, consultez [Créer des Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="c0699-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="c0699-137">Gestion de l’étendue des Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="c0699-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="c0699-138">L’étendue des Tag Helpers est contrôlée par une combinaison de `@addTagHelper`, `@removeTagHelper` et du caractère d’annulation « ! ».</span><span class="sxs-lookup"><span data-stu-id="c0699-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="c0699-139">`@addTagHelper` rend les Tag Helpers disponibles</span><span class="sxs-lookup"><span data-stu-id="c0699-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="c0699-140">Si vous créez une application web ASP.NET Core nommée *AuthoringTagHelpers* , le fichier qui suit *Views/_ViewImports.cshtml* est ajouté à votre projet :</span><span class="sxs-lookup"><span data-stu-id="c0699-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers* , the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="c0699-141">La directive `@addTagHelper` rend les Tag Helpers disponibles dans l’affichage.</span><span class="sxs-lookup"><span data-stu-id="c0699-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="c0699-142">Dans cet exemple, le fichier d’affichage est *Pages/_ViewImports.cshtml* , qui est hérité par défaut par tous les fichiers dans le dossier et les sous-dossiers *Pages*  ; les Tag Helpers sont ainsi disponibles.</span><span class="sxs-lookup"><span data-stu-id="c0699-142">In this case, the view file is *Pages/_ViewImports.cshtml* , which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="c0699-143">Le code ci-dessus utilise la syntaxe d’expressions génériques (« \* ») pour spécifier que tous les Tag Helpers dans l’assembly spécifié ( *Microsoft.AspNetCore.Mvc.TagHelpers* ) sont disponibles pour chaque fichier d’affichage du répertoire ou sous-répertoire *Views* .</span><span class="sxs-lookup"><span data-stu-id="c0699-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly ( *Microsoft.AspNetCore.Mvc.TagHelpers* ) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="c0699-144">Le premier paramètre après `@addTagHelper` spécifie les Tag Helpers à charger (nous utilisons « \* » pour tous les Tag Helpers), et le deuxième paramètre « Microsoft.AspNetCore.Mvc.TagHelpers » spécifie l’assembly qui contient les Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="c0699-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="c0699-145">*Microsoft.AspNetCore.Mvc.TagHelpers* est l’assembly des Tag Helpers ASP.NET Core intégrés.</span><span class="sxs-lookup"><span data-stu-id="c0699-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="c0699-146">Pour exposer tous les Tag Helpers inclus dans ce projet (ce qui crée un assembly nommé *AuthoringTagHelpers* ), utilisez ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="c0699-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers* ), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="c0699-147">Si votre projet contient un `EmailTagHelper` avec l’espace de noms par défaut (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), vous pouvez fournir le nom qualifié complet (FQN) des Tag helpers :</span><span class="sxs-lookup"><span data-stu-id="c0699-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="c0699-148">Pour ajouter un Tag Helper à un affichage à l’aide d’un FQN, vous ajoutez d’abord ce FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), puis le nom de l’assembly ( *AuthoringTagHelpers* ).</span><span class="sxs-lookup"><span data-stu-id="c0699-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name ( *AuthoringTagHelpers* ).</span></span> <span data-ttu-id="c0699-149">La plupart des développeurs préfèrent utiliser la syntaxe d’expressions génériques « \* ».</span><span class="sxs-lookup"><span data-stu-id="c0699-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="c0699-150">Celle-ci permet d’insérer le caractère générique « \* » en guise de suffixe dans un FQN.</span><span class="sxs-lookup"><span data-stu-id="c0699-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="c0699-151">Par exemple, chacune des directives suivantes affiche le `EmailTagHelper` :</span><span class="sxs-lookup"><span data-stu-id="c0699-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="c0699-152">Comme mentionné précédemment, l’ajout de la directive `@addTagHelper` au fichier *Views/_ViewImports.cshtml* met le Tag Helper à la disposition de tous les fichiers d’affichage inclus dans le répertoire et les sous-répertoires *Views* .</span><span class="sxs-lookup"><span data-stu-id="c0699-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="c0699-153">Vous pouvez utiliser la directive `@addTagHelper` dans des fichiers d’affichage spécifiques si vous choisissez d’exposer le Tag Helper uniquement à ces affichages.</span><span class="sxs-lookup"><span data-stu-id="c0699-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="c0699-154">`@removeTagHelper` supprime les Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="c0699-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="c0699-155">Le`@removeTagHelper` a les deux mêmes paramètres que `@addTagHelper`, et il supprime un Tag helper ajoutée précédemment.</span><span class="sxs-lookup"><span data-stu-id="c0699-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="c0699-156">Par exemple, `@removeTagHelper` appliqué à une vue supprime le Tag helper spécifié de la vue.</span><span class="sxs-lookup"><span data-stu-id="c0699-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="c0699-157">Utiliser `@removeTagHelper` dans un fichier *Views/Folder/_ViewImports.cshtml* supprime le Tag helper à partir de toutes les vues du *dossier* .</span><span class="sxs-lookup"><span data-stu-id="c0699-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder* .</span></span>

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a><span data-ttu-id="c0699-158">Contrôle de l’étendue des Tag Helpers à l’aide du fichier *_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c0699-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="c0699-159">Vous pouvez ajouter un fichier *_ViewImports.cshtml* à tout dossier d’affichage. Le moteur d’affichage applique les directives de ce fichier et du fichier *Views/_ViewImports.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="c0699-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="c0699-160">Si vous avez ajouté un fichier *Views/Home/_ViewImports.cshtml* vide pour les affichages *Home* , rien n’est modifié car le fichier *_ViewImports.cshtml* est additif.</span><span class="sxs-lookup"><span data-stu-id="c0699-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="c0699-161">Toute directive `@addTagHelper` que vous ajoutez au fichier *Views/Home/_ViewImports.cshtml* (qui n’est pas dans le fichier *Views/_ViewImports.cshtml* par défaut) expose ces Tag Helpers uniquement aux affichages inclus dans le dossier *Home* .</span><span class="sxs-lookup"><span data-stu-id="c0699-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="c0699-162">Refus d’éléments individuels</span><span class="sxs-lookup"><span data-stu-id="c0699-162">Opting out of individual elements</span></span>

<span data-ttu-id="c0699-163">Vous pouvez désactiver un Tag Helper au niveau de l’élément à l’aide du caractère d’annulation de Tag Helper (« ! »).</span><span class="sxs-lookup"><span data-stu-id="c0699-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="c0699-164">Par exemple, la validation `Email` est désactivée dans `<span>` à l’aide du caractère d’annulation de Tag Helper :</span><span class="sxs-lookup"><span data-stu-id="c0699-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="c0699-165">Vous devez appliquer le caractère d’annulation de Tag Helper à la balise d’ouverture et de fermeture.</span><span class="sxs-lookup"><span data-stu-id="c0699-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="c0699-166">(L’éditeur Visual Studio ajoute automatiquement le caractère d’annulation à la balise de fermeture quand vous en ajoutez un à la balise d’ouverture).</span><span class="sxs-lookup"><span data-stu-id="c0699-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="c0699-167">Après avoir ajouté le caractère d’annulation, l’élément et les attributs du Tag Helper ne s’affichent plus dans une police caractéristique.</span><span class="sxs-lookup"><span data-stu-id="c0699-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="c0699-168">Utilisation de `@tagHelperPrefix` pour rendre l’utilisation du Tag Helper explicite</span><span class="sxs-lookup"><span data-stu-id="c0699-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="c0699-169">La directive `@tagHelperPrefix` vous permet de spécifier une chaîne de préfixe de balise pour activer la prise en charge des Tag Helpers et rendre leur utilisation explicite.</span><span class="sxs-lookup"><span data-stu-id="c0699-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="c0699-170">Par exemple, vous pouvez ajouter le balisage suivant au fichier *Views/_ViewImports.cshtml*  :</span><span class="sxs-lookup"><span data-stu-id="c0699-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="c0699-171">Dans l’image de code ci-dessous, le préfixe du Tag Helper a la valeur `th:`, si bien que seuls les éléments qui utilisent le préfixe `th:` prennent en charge les Tag Helpers (les éléments activés pour les Tag Helpers ont une police caractéristique).</span><span class="sxs-lookup"><span data-stu-id="c0699-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="c0699-172">Les éléments `<label>` et `<input>` ont le préfixe du Tag Helper et sont activés, à la différence de l’élément `<span>`.</span><span class="sxs-lookup"><span data-stu-id="c0699-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![image](intro/_static/thp.png)

<span data-ttu-id="c0699-174">Les mêmes règles de hiérarchie qui s’appliquent à `@addTagHelper` s’appliquent aussi à `@tagHelperPrefix`.</span><span class="sxs-lookup"><span data-stu-id="c0699-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="c0699-175">Tag Helpers à fermeture automatique</span><span class="sxs-lookup"><span data-stu-id="c0699-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="c0699-176">De nombreux Tag Helpers ne peuvent pas être utilisés en tant que balises à fermeture automatique.</span><span class="sxs-lookup"><span data-stu-id="c0699-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="c0699-177">Certains Tag Helpers sont conçus en tant que balises à fermeture automatique.</span><span class="sxs-lookup"><span data-stu-id="c0699-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="c0699-178">L’utilisation d’un Tag Helper qui n’a pas été conçu pour être à fermeture automatique supprime la sortie rendue.</span><span class="sxs-lookup"><span data-stu-id="c0699-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="c0699-179">La fermeture automatique d’un Tag Helper aboutit à une balise à fermeture automatique dans la sortie rendue.</span><span class="sxs-lookup"><span data-stu-id="c0699-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="c0699-180">Pour plus d’informations, consultez [cette remarque](xref:mvc/views/tag-helpers/authoring#self-closing) dans [Création de Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="c0699-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="c-in-tag-helpers-attributedeclaration"></a><span data-ttu-id="c0699-181">Déclaration de l’attribut/la déclaration dans C#</span><span class="sxs-lookup"><span data-stu-id="c0699-181">C# in Tag Helpers attribute/declaration</span></span> 

<span data-ttu-id="c0699-182">Les tag helpers n’autorisent pas C# dans l’attribut ou la zone de déclaration de balise de l’élément.</span><span class="sxs-lookup"><span data-stu-id="c0699-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span></span> <span data-ttu-id="c0699-183">Par exemple, le code suivant n’est pas valide :</span><span class="sxs-lookup"><span data-stu-id="c0699-183">For example, the following code is not valid:</span></span>

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

<span data-ttu-id="c0699-184">Le code précédent peut être écrit comme suit :</span><span class="sxs-lookup"><span data-stu-id="c0699-184">The preceding code can be written as:</span></span>

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="c0699-185">Prise en charge IntelliSense des Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="c0699-185">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="c0699-186">Quand vous créez une nouvelle ASP.NET Core application Web dans Visual Studio, elle ajoute le package NuGet «Microsoft. AspNetCore. Razor . Outils».</span><span class="sxs-lookup"><span data-stu-id="c0699-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.Razor.Tools".</span></span> <span data-ttu-id="c0699-187">Il s’agit du package qui ajoute les outils de Tag Helper.</span><span class="sxs-lookup"><span data-stu-id="c0699-187">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="c0699-188">Envisagez d’écrire un élément `<label>` HTML.</span><span class="sxs-lookup"><span data-stu-id="c0699-188">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="c0699-189">Dès que vous entrez `<l` dans l’éditeur Visual Studio, IntelliSense affiche les éléments correspondants :</span><span class="sxs-lookup"><span data-stu-id="c0699-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="c0699-191">Non seulement vous obtenez l’aide HTML, mais aussi l’icône (le « @" symbol with "<> » en dessous).</span><span class="sxs-lookup"><span data-stu-id="c0699-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![image](intro/_static/tagSym.png)

<span data-ttu-id="c0699-193">Identifie l’élément comme étant ciblé par les Tag Helpers.</span><span class="sxs-lookup"><span data-stu-id="c0699-193">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="c0699-194">Les éléments HTML purs (comme `fieldset`) présentent l’icône « <> ».</span><span class="sxs-lookup"><span data-stu-id="c0699-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="c0699-195">Une balise `<label>` HTML pure affiche la balise HTML (avec le thème de couleur Visual Studio par défaut) dans une police marron, les attributs en rouge et les valeurs d’attribut en bleu.</span><span class="sxs-lookup"><span data-stu-id="c0699-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![image](intro/_static/LableHtmlTag.png)

<span data-ttu-id="c0699-197">Après avoir entré `<label`, IntelliSense répertorie les attributs HTML/CSS disponibles et les attributs ciblés par les Tag Helpers :</span><span class="sxs-lookup"><span data-stu-id="c0699-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![image](intro/_static/labelattr.png)

<span data-ttu-id="c0699-199">La saisie semi-automatique des instructions par IntelliSense vous permet d’appuyer sur la touche Tab pour compléter automatiquement l’instruction avec la valeur sélectionnée :</span><span class="sxs-lookup"><span data-stu-id="c0699-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![image](intro/_static/stmtcomplete.png)

<span data-ttu-id="c0699-201">Dès qu’un attribut de Tag Helper est entré, les polices de la balise et de l’attribut changent.</span><span class="sxs-lookup"><span data-stu-id="c0699-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="c0699-202">En utilisant le thème de couleur « Bleu » ou « Clair » par défaut de Visual Studio, la police est en violet gras.</span><span class="sxs-lookup"><span data-stu-id="c0699-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="c0699-203">Si vous utilisez le thème « Foncé », la police est en bleu-vert gras.</span><span class="sxs-lookup"><span data-stu-id="c0699-203">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="c0699-204">Les images de ce document ont été prises à l’aide du thème par défaut.</span><span class="sxs-lookup"><span data-stu-id="c0699-204">The images in this document were taken using the default theme.</span></span>

![image](intro/_static/labelaspfor2.png)

<span data-ttu-id="c0699-206">Vous pouvez entrer le raccourci *CompleteWord* Visual Studio (Ctrl+Espace [par défaut](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio)) à l’intérieur des guillemets doubles ("") pour passer en C#, de la même façon que dans une classe C#.</span><span class="sxs-lookup"><span data-stu-id="c0699-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="c0699-207">IntelliSense affiche toutes les méthodes et propriétés dans le modèle de page.</span><span class="sxs-lookup"><span data-stu-id="c0699-207">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="c0699-208">Les méthodes et propriétés sont disponibles car le type de propriété est `ModelExpression`.</span><span class="sxs-lookup"><span data-stu-id="c0699-208">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="c0699-209">Dans l’image ci-dessous, je modifie l’affichage `Register`, donc `RegisterViewModel` est disponible.</span><span class="sxs-lookup"><span data-stu-id="c0699-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![image](intro/_static/intellemail.png)

<span data-ttu-id="c0699-211">IntelliSense répertorie les propriétés et méthodes disponibles pour le modèle dans la page.</span><span class="sxs-lookup"><span data-stu-id="c0699-211">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="c0699-212">Le riche environnement IntelliSense vous aide à sélectionner la classe CSS :</span><span class="sxs-lookup"><span data-stu-id="c0699-212">The rich IntelliSense environment helps you select the CSS class:</span></span>

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="c0699-215">Comparaison des Tag Helpers aux HTML Helpers</span><span class="sxs-lookup"><span data-stu-id="c0699-215">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="c0699-216">Les tag helpers s’attachent aux éléments HTML dans les Razor vues, tandis que les [applications auxiliaires html](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) sont appelées comme des méthodes intercalées avec du code HTML dans les Razor vues.</span><span class="sxs-lookup"><span data-stu-id="c0699-216">Tag Helpers attach to HTML elements in Razor views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in Razor views.</span></span> <span data-ttu-id="c0699-217">Examinez le Razor balisage suivant, qui crée une étiquette HTML avec la classe CSS « Caption » :</span><span class="sxs-lookup"><span data-stu-id="c0699-217">Consider the following Razor markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="c0699-218">Le symbole at ( `@` ) indique qu' Razor il s’agit du début du code.</span><span class="sxs-lookup"><span data-stu-id="c0699-218">The at (`@`) symbol tells Razor this is the start of code.</span></span> <span data-ttu-id="c0699-219">Les deux paramètres suivants (« FirstName » et « First Name: ») sont des chaînes, par conséquent, [IntelliSense](/visualstudio/ide/using-intellisense) ne sert à rien.</span><span class="sxs-lookup"><span data-stu-id="c0699-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="c0699-220">Le dernier argument :</span><span class="sxs-lookup"><span data-stu-id="c0699-220">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="c0699-221">Est un objet anonyme utilisé pour représenter des attributs.</span><span class="sxs-lookup"><span data-stu-id="c0699-221">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="c0699-222">Étant donné que `class` est un mot clé réservé en C#, vous utilisez le symbole `@` pour forcer le code C# à interpréter `@class=` comme un symbole (nom de propriété).</span><span class="sxs-lookup"><span data-stu-id="c0699-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="c0699-223">Pour un concepteur frontal (une personne connaissant le HTML/CSS/JavaScript et d’autres technologies clientes, mais qui ne connaît pas C# et Razor ), la majeure partie de la ligne est étrangère.</span><span class="sxs-lookup"><span data-stu-id="c0699-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and Razor), most of the line is foreign.</span></span> <span data-ttu-id="c0699-224">La ligne entière doit être créée sans l’aide d’IntelliSense.</span><span class="sxs-lookup"><span data-stu-id="c0699-224">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="c0699-225">Avec `LabelTagHelper`, le même balisage peut s’écrire ainsi :</span><span class="sxs-lookup"><span data-stu-id="c0699-225">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="c0699-226">Avec la version Tag Helper, dès que vous entrez `<l` dans l’éditeur Visual Studio, IntelliSense affiche les éléments correspondants :</span><span class="sxs-lookup"><span data-stu-id="c0699-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="c0699-228">IntelliSense vous aide à écrire la ligne entière.</span><span class="sxs-lookup"><span data-stu-id="c0699-228">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="c0699-229">L’image de code suivante montre la partie formulaire de la vue *views/Account/Register. cshtml* Razor générée à partir du modèle MVC ASP.net 4.5. x inclus dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c0699-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* Razor view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![image](intro/_static/regCS.png)

<span data-ttu-id="c0699-231">L’éditeur Visual Studio présente le code C# avec un arrière-plan gris.</span><span class="sxs-lookup"><span data-stu-id="c0699-231">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="c0699-232">Par exemple, le HTML Helper `AntiForgeryToken` :</span><span class="sxs-lookup"><span data-stu-id="c0699-232">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="c0699-233">est présenté avec un arrière-plan gris.</span><span class="sxs-lookup"><span data-stu-id="c0699-233">is displayed with a grey background.</span></span> <span data-ttu-id="c0699-234">La plupart du balisage dans l’affichage Register est en C#.</span><span class="sxs-lookup"><span data-stu-id="c0699-234">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="c0699-235">Comparez-le à l’approche équivalente qui utilise des Tag Helpers :</span><span class="sxs-lookup"><span data-stu-id="c0699-235">Compare that to the equivalent approach using Tag Helpers:</span></span>

![image](intro/_static/regTH.png)

<span data-ttu-id="c0699-237">Le balisage est beaucoup plus claire et facile à lire, modifier et gérer qu’avec l’approche des HTML Helpers.</span><span class="sxs-lookup"><span data-stu-id="c0699-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="c0699-238">Le code C# est réduit au minimum que le serveur doit savoir.</span><span class="sxs-lookup"><span data-stu-id="c0699-238">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="c0699-239">L’éditeur Visual Studio présente le balisage ciblé par un Tag Helper dans une police caractéristique.</span><span class="sxs-lookup"><span data-stu-id="c0699-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="c0699-240">Examinez le groupe *Email*  :</span><span class="sxs-lookup"><span data-stu-id="c0699-240">Consider the *Email* group:</span></span>

[!code-cshtml[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="c0699-241">Chacun des attributs « asp- » a la valeur « Email », mais « Email » n’est pas une chaîne.</span><span class="sxs-lookup"><span data-stu-id="c0699-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="c0699-242">Dans ce contexte, « Email » est la propriété de l’expression du modèle C# pour `RegisterViewModel`.</span><span class="sxs-lookup"><span data-stu-id="c0699-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="c0699-243">L’éditeur Visual Studio vous aide à écrire **tout** le balisage dans l’approche Tag Helpers du formulaire d’inscription, tandis que Visual Studio ne fournit aucune aide pour la plupart du code dans l’approche HTML Helpers.</span><span class="sxs-lookup"><span data-stu-id="c0699-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="c0699-244">[Prise en charge IntelliSense des Tag Helpers](#intellisense-support-for-tag-helpers) décrit précisément l’utilisation de Tag Helpers dans l’éditeur Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c0699-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="c0699-245">Comparaison des Tag Helpers aux contrôles de serveur web</span><span class="sxs-lookup"><span data-stu-id="c0699-245">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="c0699-246">Les Tag Helpers ne sont pas propriétaires de l’élément auquel ils sont associés ; ils participent simplement au rendu de l’élément et du contenu.</span><span class="sxs-lookup"><span data-stu-id="c0699-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="c0699-247"><https://docs.microsoft.com/previous-versions/dotnet/netframework-3.0/7698y1f0(v=vs.85)>Les ASP.net sont déclarés et appelés sur une page.</span><span class="sxs-lookup"><span data-stu-id="c0699-247">ASP.NET <https://docs.microsoft.com/previous-versions/dotnet/netframework-3.0/7698y1f0(v=vs.85)> are declared and invoked on a page.</span></span>

* <span data-ttu-id="c0699-248"><https://docs.microsoft.com/previous-versions/zsyt68f1(v=vs.140)> avoir un cycle de vie non trivial qui peut compliquer le développement et le débogage.</span><span class="sxs-lookup"><span data-stu-id="c0699-248"><https://docs.microsoft.com/previous-versions/zsyt68f1(v=vs.140)> have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="c0699-249">Les contrôles de serveur web vous permettent d’ajouter des fonctionnalités aux éléments DOM (Document Object Model) à l’aide d’un contrôle client.</span><span class="sxs-lookup"><span data-stu-id="c0699-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="c0699-250">Les Tag Helpers n’ont pas de DOM.</span><span class="sxs-lookup"><span data-stu-id="c0699-250">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="c0699-251">Les contrôles de serveur web incluent une détection automatique du navigateur.</span><span class="sxs-lookup"><span data-stu-id="c0699-251">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="c0699-252">Les Tag Helpers n’ont pas connaissance du navigateur.</span><span class="sxs-lookup"><span data-stu-id="c0699-252">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="c0699-253">Plusieurs Tag Helpers peuvent agir sur le même élément (consultez [Éviter les conflits de Tag Helpers](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts)) alors que généralement vous ne pouvez pas composer des contrôles de serveur web.</span><span class="sxs-lookup"><span data-stu-id="c0699-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="c0699-254">Les Tag Helpers peuvent modifier la balise et le contenu des éléments HTML auxquels ils s’étendent, mais pas modifier directement quoi que ce soit d’autre dans une page.</span><span class="sxs-lookup"><span data-stu-id="c0699-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="c0699-255">Les contrôles de serveur web ont une étendue moins spécifique et peuvent effectuer des actions qui affectent d’autres parties de votre page, ce qui peut entraîner des effets secondaires involontaires.</span><span class="sxs-lookup"><span data-stu-id="c0699-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="c0699-256">Les contrôles de serveur web utilisent des convertisseurs de type pour convertir les chaînes en objets.</span><span class="sxs-lookup"><span data-stu-id="c0699-256">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="c0699-257">Avec les Tag Helpers, vous travaillez en mode natif en C#, donc vous n’avez pas besoin d’effectuer de conversions de type.</span><span class="sxs-lookup"><span data-stu-id="c0699-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="c0699-258">Les contrôles de serveur web utilisent [System.ComponentModel](/dotnet/api/system.componentmodel) pour implémenter le comportement au moment de l’exécution et au moment du design des composants et des contrôles.</span><span class="sxs-lookup"><span data-stu-id="c0699-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="c0699-259">`System.ComponentModel` inclut les classes et les interfaces de base servant à l’implémentation des attributs et des convertisseurs de type, à la liaison à des sources de données et à la gestion des licences des composants.</span><span class="sxs-lookup"><span data-stu-id="c0699-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="c0699-260">Comparez-les aux Tag Helpers, qui sont généralement dérivés de `TagHelper` et à la classe de base `TagHelper` qui expose uniquement deux méthodes, `Process` et `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="c0699-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="c0699-261">Personnalisation de la police des éléments Tag Helper</span><span class="sxs-lookup"><span data-stu-id="c0699-261">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="c0699-262">Vous pouvez personnaliser la police et la coloration à partir des options **Outils**  >  **Options**  >  **Environment**  >  **polices et couleurs** de l’environnement :</span><span class="sxs-lookup"><span data-stu-id="c0699-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors** :</span></span>

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="c0699-264">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="c0699-264">Additional resources</span></span>

* [<span data-ttu-id="c0699-265">Créer des Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="c0699-265">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="c0699-266">Utilisation des formulaires</span><span class="sxs-lookup"><span data-stu-id="c0699-266">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="c0699-267">[TagHelperSamples sur GitHub](https://github.com/dpaquette/TagHelperSamples) contient des exemples de Tag Helpers à utiliser avec [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="c0699-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span></span>
