---
title: Tag Helper Ancre dans ASP.NET Core
author: pkellner
description: Découvrez les attributs ASP.NET Core Tag Helper Ancre et le rôle joué par chaque attribut dans l’extension du comportement de la balise d’ancrage HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
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
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 2e49c545b0d343475ce44a636a6ae66324f9d3bf
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587201"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="34850-103">Tag Helper Ancre dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34850-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="34850-104">Par [Peter Kellner](https://peterkellner.net) et [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="34850-104">By [Peter Kellner](https://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="34850-105">Le [Tag Helper Ancre](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) améliore la balise d’ancrage HTML standard (`<a ... ></a>`) en ajoutant de nouveaux attributs.</span><span class="sxs-lookup"><span data-stu-id="34850-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="34850-106">Par convention, les noms d’attribut commencent par `asp-`.</span><span class="sxs-lookup"><span data-stu-id="34850-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="34850-107">La valeur d’attribut de l’élément d’ancrage rendu `href` est déterminée par les valeurs des attributs `asp-`.</span><span class="sxs-lookup"><span data-stu-id="34850-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="34850-108">Pour avoir une vue d’ensemble de Tag Helpers, consultez <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="34850-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="34850-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="34850-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="34850-110">*SpeakerController* est utilisé dans les exemples dans ce document :</span><span class="sxs-lookup"><span data-stu-id="34850-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a><span data-ttu-id="34850-111">Attributs de Tag Helper Ancre</span><span class="sxs-lookup"><span data-stu-id="34850-111">Anchor Tag Helper attributes</span></span>

### <a name="asp-controller"></a><span data-ttu-id="34850-112">asp-controller</span><span class="sxs-lookup"><span data-stu-id="34850-112">asp-controller</span></span>

<span data-ttu-id="34850-113">L’attribut [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) assigne le contrôleur utilisé pour générer l’URL.</span><span class="sxs-lookup"><span data-stu-id="34850-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="34850-114">Le balisage suivant répertorie tous les présentateurs :</span><span class="sxs-lookup"><span data-stu-id="34850-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="34850-115">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="34850-116">Si l’attribut `asp-controller` est spécifié et que `asp-action` ne l’est pas, la valeur par défaut `asp-action` est l’action du contrôleur associée à la vue en cours d’exécution.</span><span class="sxs-lookup"><span data-stu-id="34850-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="34850-117">Si `asp-action` est omis du balisage précédent, et le Tag Helper Ancre est utilisé dans la vue *Index* de *HomeController* (*/Home*), le code HTML généré est :</span><span class="sxs-lookup"><span data-stu-id="34850-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a><span data-ttu-id="34850-118">asp-action</span><span class="sxs-lookup"><span data-stu-id="34850-118">asp-action</span></span>

<span data-ttu-id="34850-119">La valeur de l’attribut [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) représente le nom d’action du contrôleur inclus dans l’attribut `href` généré.</span><span class="sxs-lookup"><span data-stu-id="34850-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="34850-120">Le balisage suivant définit la valeur de l’attribut `href` généré sur la page d’évaluations du présentateur :</span><span class="sxs-lookup"><span data-stu-id="34850-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="34850-121">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="34850-122">Si aucun attribut `asp-controller` n’est spécifié, le contrôleur par défaut qui appelle la vue exécutant la vue actuelle est utilisé.</span><span class="sxs-lookup"><span data-stu-id="34850-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="34850-123">Si la valeur de l’attribut `asp-action` est `Index`, aucune action n’est ajoutée à l’URL, ce qui aboutit à l’appel de l’action `Index` par défaut.</span><span class="sxs-lookup"><span data-stu-id="34850-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="34850-124">L’action spécifiée (ou par défaut) doit exister dans le contrôleur référencé dans `asp-controller`.</span><span class="sxs-lookup"><span data-stu-id="34850-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

### <a name="asp-route-value"></a><span data-ttu-id="34850-125">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="34850-125">asp-route-{value}</span></span>

<span data-ttu-id="34850-126">L’attribut [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) active un préfixe d’itinéraire générique.</span><span class="sxs-lookup"><span data-stu-id="34850-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="34850-127">Toute valeur occupant l’espace réservé `{value}` est interprétée comme un paramètre d’itinéraire potentiel.</span><span class="sxs-lookup"><span data-stu-id="34850-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="34850-128">Si aucune route par défaut n’est trouvée, ce préfixe de routage est ajouté à l’attribut `href` généré en tant que valeur et paramètre de requête.</span><span class="sxs-lookup"><span data-stu-id="34850-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="34850-129">Dans le cas contraire, il est remplacé dans le modèle de routage.</span><span class="sxs-lookup"><span data-stu-id="34850-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="34850-130">Examinons l’action du contrôleur ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="34850-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="34850-131">Avec un modèle d’itinéraire par défaut défini dans *Startup.Configure* :</span><span class="sxs-lookup"><span data-stu-id="34850-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="34850-132">La vue MVC utilise le modèle, fourni par l’action, comme suit :</span><span class="sxs-lookup"><span data-stu-id="34850-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="34850-133">L’espace réservé `{id?}` de l’itinéraire par défaut a été mis en correspondance.</span><span class="sxs-lookup"><span data-stu-id="34850-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="34850-134">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="34850-135">Supposons que le préfixe d’itinéraire ne fait pas partie du modèle de routage correspondant, comme dans la vue MVC suivante :</span><span class="sxs-lookup"><span data-stu-id="34850-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="34850-136">Le code HTML suivant est généré, car `speakerid` n’a pas été trouvé dans l’itinéraire correspondant :</span><span class="sxs-lookup"><span data-stu-id="34850-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="34850-137">Si `asp-controller` ou `asp-action` ne sont pas spécifiés, le même traitement par défaut est appliqué que dans l’attribut `asp-route`.</span><span class="sxs-lookup"><span data-stu-id="34850-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

### <a name="asp-route"></a><span data-ttu-id="34850-138">asp-route</span><span class="sxs-lookup"><span data-stu-id="34850-138">asp-route</span></span>

<span data-ttu-id="34850-139">L’attribut [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) est utilisé pour créer une URL reliant directement à un itinéraire nommé.</span><span class="sxs-lookup"><span data-stu-id="34850-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="34850-140">À l’aide des [attributs de routage](xref:mvc/controllers/routing#attribute-routing), un itinéraire peut être nommé comme indiqué dans `SpeakerController` et utilisé dans son action `Evaluations` :</span><span class="sxs-lookup"><span data-stu-id="34850-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="34850-141">Dans le balisage suivant, l’attribut `asp-route` fait référence à l’itinéraire nommé :</span><span class="sxs-lookup"><span data-stu-id="34850-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="34850-142">Le Tag Helper Ancre génère un itinéraire directement vers cette action de contrôleur à l’aide de l’URL */Speaker/Evaluations*.</span><span class="sxs-lookup"><span data-stu-id="34850-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="34850-143">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="34850-144">Si `asp-controller` ou `asp-action` est spécifié en plus de `asp-route`, la route générée ne correspond peut-être pas à ce que vous attendez.</span><span class="sxs-lookup"><span data-stu-id="34850-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="34850-145">`asp-route` ne doit pas être utilisé avec les attributs `asp-controller` ou `asp-action` afin d’éviter un conflit de routage.</span><span class="sxs-lookup"><span data-stu-id="34850-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

### <a name="asp-all-route-data"></a><span data-ttu-id="34850-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="34850-146">asp-all-route-data</span></span>

<span data-ttu-id="34850-147">L’attribut [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) prend en charge la création d’un dictionnaire de paires clé-valeur.</span><span class="sxs-lookup"><span data-stu-id="34850-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="34850-148">La clé est le nom du paramètre et la valeur est la valeur du paramètre.</span><span class="sxs-lookup"><span data-stu-id="34850-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="34850-149">Dans l’exemple suivant, un dictionnaire est initialisé et passé à une Razor vue.</span><span class="sxs-lookup"><span data-stu-id="34850-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="34850-150">Les données peuvent également être transmises avec votre modèle.</span><span class="sxs-lookup"><span data-stu-id="34850-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="34850-151">Le code précédent génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="34850-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="34850-152">Le dictionnaire `asp-all-route-data` est aplati pour produire une chaîne de requête conforme aux exigences de l’action `Evaluations` surchargée :</span><span class="sxs-lookup"><span data-stu-id="34850-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="34850-153">Si des clés dans le dictionnaire correspondent aux paramètres d’itinéraire, ces valeurs sont substituées dans l’itinéraire comme il convient.</span><span class="sxs-lookup"><span data-stu-id="34850-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="34850-154">Les autres valeurs non correspondantes sont générées en tant que paramètres de la requête.</span><span class="sxs-lookup"><span data-stu-id="34850-154">The other non-matching values are generated as request parameters.</span></span>

### <a name="asp-fragment"></a><span data-ttu-id="34850-155">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="34850-155">asp-fragment</span></span>

<span data-ttu-id="34850-156">L’attribut [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) définit un fragment d’URL à ajouter à l’URL.</span><span class="sxs-lookup"><span data-stu-id="34850-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="34850-157">Le Tag Helper Ancre ajoute le caractère de hachage (#).</span><span class="sxs-lookup"><span data-stu-id="34850-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="34850-158">Examinons le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="34850-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="34850-159">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="34850-160">Les balises de hachage sont utiles lors de la création des applications côté client.</span><span class="sxs-lookup"><span data-stu-id="34850-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="34850-161">Elles peuvent servir à faciliter le marquage et la recherche en JavaScript, par exemple.</span><span class="sxs-lookup"><span data-stu-id="34850-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

### <a name="asp-area"></a><span data-ttu-id="34850-162">asp-area</span><span class="sxs-lookup"><span data-stu-id="34850-162">asp-area</span></span>

<span data-ttu-id="34850-163">L’attribut [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) définit le nom de la zone utilisé pour définir l’itinéraire approprié.</span><span class="sxs-lookup"><span data-stu-id="34850-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="34850-164">Les exemples suivants décrivent la façon dont l’attribut `asp-area` entraîne un remappage des itinéraires.</span><span class="sxs-lookup"><span data-stu-id="34850-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

#### <a name="usage-in-razor-pages"></a><span data-ttu-id="34850-165">Utilisation dans les Razor pages</span><span class="sxs-lookup"><span data-stu-id="34850-165">Usage in Razor Pages</span></span>

<span data-ttu-id="34850-166">Razor Les zones de pages sont prises en charge dans ASP.NET Core 2,1 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="34850-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="34850-167">Considérez la hiérarchie de répertoires suivante :</span><span class="sxs-lookup"><span data-stu-id="34850-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="34850-168">**{Nom du projet}**</span><span class="sxs-lookup"><span data-stu-id="34850-168">**{Project name}**</span></span>
  * <span data-ttu-id="34850-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="34850-169">**wwwroot**</span></span>
  * <span data-ttu-id="34850-170">**Zones (Areas)**</span><span class="sxs-lookup"><span data-stu-id="34850-170">**Areas**</span></span>
    * <span data-ttu-id="34850-171">**Sessions**</span><span class="sxs-lookup"><span data-stu-id="34850-171">**Sessions**</span></span>
      * <span data-ttu-id="34850-172">**Pages**</span><span class="sxs-lookup"><span data-stu-id="34850-172">**Pages**</span></span>
        * <span data-ttu-id="34850-173">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="34850-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="34850-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="34850-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="34850-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="34850-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="34850-176">**Pages**</span><span class="sxs-lookup"><span data-stu-id="34850-176">**Pages**</span></span>

<span data-ttu-id="34850-177">Le balisage pour référencer la page d' *index* de la zone *sessions* Razor est :</span><span class="sxs-lookup"><span data-stu-id="34850-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="34850-178">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="34850-179">Pour prendre en charge les zones dans une Razor application de pages, effectuez l’une des opérations suivantes dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="34850-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="34850-180">Définir la [version de compatibilité](xref:mvc/compatibility-version) sur 2.1 ou au-dessus.</span><span class="sxs-lookup"><span data-stu-id="34850-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="34850-181">Définissez la propriété [ Razor PagesOptions. AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) sur `true` :</span><span class="sxs-lookup"><span data-stu-id="34850-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a><span data-ttu-id="34850-182">Utilisation dans MVC</span><span class="sxs-lookup"><span data-stu-id="34850-182">Usage in MVC</span></span>

<span data-ttu-id="34850-183">Considérez la hiérarchie de répertoires suivante :</span><span class="sxs-lookup"><span data-stu-id="34850-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="34850-184">**{Nom du projet}**</span><span class="sxs-lookup"><span data-stu-id="34850-184">**{Project name}**</span></span>
  * <span data-ttu-id="34850-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="34850-185">**wwwroot**</span></span>
  * <span data-ttu-id="34850-186">**Zones (Areas)**</span><span class="sxs-lookup"><span data-stu-id="34850-186">**Areas**</span></span>
    * <span data-ttu-id="34850-187">**Blogs**</span><span class="sxs-lookup"><span data-stu-id="34850-187">**Blogs**</span></span>
      * <span data-ttu-id="34850-188">**Controllers**</span><span class="sxs-lookup"><span data-stu-id="34850-188">**Controllers**</span></span>
        * <span data-ttu-id="34850-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="34850-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="34850-190">**Views**</span><span class="sxs-lookup"><span data-stu-id="34850-190">**Views**</span></span>
        * <span data-ttu-id="34850-191">**Page d'accueil**</span><span class="sxs-lookup"><span data-stu-id="34850-191">**Home**</span></span>
          * <span data-ttu-id="34850-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="34850-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="34850-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="34850-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="34850-194">*\_ViewStart. cshtml*</span><span class="sxs-lookup"><span data-stu-id="34850-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="34850-195">**Controllers**</span><span class="sxs-lookup"><span data-stu-id="34850-195">**Controllers**</span></span>

<span data-ttu-id="34850-196">L’affectation de la valeur « Blogs » à `asp-area` préfixe le répertoire *Areas/Blogs* dans les itinéraires des vues et contrôleurs associés pour cette balise d’ancrage.</span><span class="sxs-lookup"><span data-stu-id="34850-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="34850-197">Le balisage pour faire référence à la vue *AboutBlog* est :</span><span class="sxs-lookup"><span data-stu-id="34850-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="34850-198">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="34850-199">Pour prendre en charge les zones dans une application MVC, le modèle de routage doit inclure une référence à la zone si elle existe.</span><span class="sxs-lookup"><span data-stu-id="34850-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="34850-200">Ce modèle est représenté par le deuxième paramètre de l' `routes.MapRoute` appel de la méthode dans *Startup.Configurer*:</span><span class="sxs-lookup"><span data-stu-id="34850-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a><span data-ttu-id="34850-201">asp-protocol</span><span class="sxs-lookup"><span data-stu-id="34850-201">asp-protocol</span></span>

<span data-ttu-id="34850-202">L’attribut [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) permet de spécifier un protocole (tel que `https`) dans l’URL.</span><span class="sxs-lookup"><span data-stu-id="34850-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="34850-203">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="34850-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="34850-204">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="34850-205">Le nom d’hôte dans l’exemple est localhost.</span><span class="sxs-lookup"><span data-stu-id="34850-205">The host name in the example is localhost.</span></span> <span data-ttu-id="34850-206">Le Tag Helper Ancre utilise le domaine public du site web lors de la génération de l’URL.</span><span class="sxs-lookup"><span data-stu-id="34850-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

### <a name="asp-host"></a><span data-ttu-id="34850-207">asp-host</span><span class="sxs-lookup"><span data-stu-id="34850-207">asp-host</span></span>

<span data-ttu-id="34850-208">L’attribut [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) est destiné à spécifier un nom d’hôte dans votre URL.</span><span class="sxs-lookup"><span data-stu-id="34850-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="34850-209">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="34850-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="34850-210">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a><span data-ttu-id="34850-211">asp-page</span><span class="sxs-lookup"><span data-stu-id="34850-211">asp-page</span></span>

<span data-ttu-id="34850-212">L’attribut [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) est utilisé avec les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="34850-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="34850-213">Utilisez-le pour définir la valeur d’attribut `href` d’une balise d’ancrage sur une page spécifique.</span><span class="sxs-lookup"><span data-stu-id="34850-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="34850-214">En ajoutant une barre oblique (« / ») comme préfixe au nom de la page, vous créez l’URL.</span><span class="sxs-lookup"><span data-stu-id="34850-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="34850-215">L’exemple suivant pointe vers la page participant Razor :</span><span class="sxs-lookup"><span data-stu-id="34850-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="34850-216">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="34850-217">L’attribut `asp-page` et les attributs `asp-route`, `asp-controller` et `asp-action` s’excluent mutuellement.</span><span class="sxs-lookup"><span data-stu-id="34850-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="34850-218">Toutefois, `asp-page` peut être utilisé avec `asp-route-{value}` pour contrôler le routage, comme illustré dans le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="34850-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="34850-219">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a><span data-ttu-id="34850-220">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="34850-220">asp-page-handler</span></span>

<span data-ttu-id="34850-221">L’attribut [asp-page-Handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) est utilisé avec les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="34850-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="34850-222">Il est destiné à la liaison à des gestionnaires de page spécifiques.</span><span class="sxs-lookup"><span data-stu-id="34850-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="34850-223">Prenons le gestionnaire de page suivant :</span><span class="sxs-lookup"><span data-stu-id="34850-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="34850-224">Le balisage associé au modèle de page est lié au gestionnaire de page `OnGetProfile`.</span><span class="sxs-lookup"><span data-stu-id="34850-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="34850-225">Notez que le préfixe `On<Verb>` du nom de la méthode du gestionnaire de page est omis dans la valeur d’attribut `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="34850-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="34850-226">Lorsque la méthode est asynchrone, le suffixe `Async` est également omis.</span><span class="sxs-lookup"><span data-stu-id="34850-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="34850-227">Code HTML généré :</span><span class="sxs-lookup"><span data-stu-id="34850-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="34850-228">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="34850-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
