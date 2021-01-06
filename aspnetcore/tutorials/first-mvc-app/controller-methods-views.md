---
title: Partie 6, méthodes et vues de contrôleur dans ASP.NET Core
author: rick-anderson
description: 'Partie 6 : ajouter un modèle à une application ASP.NET Core MVC'
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: b4850821317b6907452793ef09194844c90c0137
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93050769"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a><span data-ttu-id="3ceb4-103">Partie 6, méthodes et vues de contrôleur dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3ceb4-103">Part 6, controller methods and views in ASP.NET Core</span></span>

<span data-ttu-id="3ceb4-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3ceb4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3ceb4-105">Nous avons une bonne ébauche de l’application de films, mais sa présentation n’est pas idéale, par exemple, **ReleaseDate** devrait être écrit en deux mots.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-105">We have a good start to the movie app, but the presentation isn't ideal, for example, **ReleaseDate** should be two words.</span></span>

![Vue d’index : Release Date apparaît en un seul mot (sans espace) et chaque date de sortie d’un film a comme heure « 12 AM »](working-with-sql/_static/m55.png)

<span data-ttu-id="3ceb4-107">Ouvrez le fichier *Models/Movie.cs*, puis ajoutez les lignes en surbrillance ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="3ceb4-107">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

<span data-ttu-id="3ceb4-108">Nous abordons [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) dans le prochain tutoriel.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-108">We cover [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) in the next tutorial.</span></span> <span data-ttu-id="3ceb4-109">L’attribut [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) spécifie les éléments à afficher pour le nom d’un champ (dans le cas présent, « Release Date » au lieu de « ReleaseDate »).</span><span class="sxs-lookup"><span data-stu-id="3ceb4-109">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="3ceb4-110">L’attribut [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) spécifie le type des données (Date). Les informations d’heures stockées dans le champ ne s’affichent donc pas.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-110">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="3ceb4-111">L’annotation de données `[Column(TypeName = "decimal(18, 2)")]` est nécessaire pour qu’Entity Framework Core puisse correctement mapper `Price` en devise dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="3ceb4-112">Pour plus d’informations, consultez [Types de données](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="3ceb4-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="3ceb4-113">Accédez au contrôleur `Movies` et maintenez le pointeur de la souris sur un lien **Edit** pour afficher l’URL cible.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-113">Browse to the `Movies` controller and hold the mouse pointer over an **Edit** link to see the target URL.</span></span>

![Fenêtre de navigateur avec la souris sur le lien Edit et l’URL de lien https://localhost:5001/Movies/Edit/5 affichée](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

<span data-ttu-id="3ceb4-115">Les liens **Edit**, **Details** et **Delete** sont générés par le Tag Helper Anchor Core MVC dans le fichier *Views/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-115">The **Edit**, **Details**, and **Delete** links are generated by the Core MVC Anchor Tag Helper in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

<span data-ttu-id="3ceb4-116">Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-116">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="3ceb4-117">Dans le code ci-dessus, le `AnchorTagHelper` génère dynamiquement la `href` valeur de l’attribut HTML à partir de la méthode d’action du contrôleur et de l’ID de l’itinéraire. Vous utilisez **afficher la source** à partir de votre navigateur favori ou utiliser les outils de développement pour examiner le balisage généré.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-117">In the code above, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the controller action method and route id. You use **View Source** from your favorite browser or use the developer tools to examine the generated markup.</span></span> <span data-ttu-id="3ceb4-118">Une partie du code HTML généré est affichée ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="3ceb4-118">A portion of the generated HTML is shown below:</span></span>

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

<span data-ttu-id="3ceb4-119">Rappelez-vous le format du [routage](xref:mvc/controllers/routing) défini dans le fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="3ceb4-119">Recall the format for [routing](xref:mvc/controllers/routing) set in the *Startup.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="3ceb4-120">ASP.NET Core traduit `https://localhost:5001/Movies/Edit/4` en une requête à la méthode d’action `Edit` du contrôleur `Movies` avec un paramètre `Id` de 4.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-120">ASP.NET Core translates `https://localhost:5001/Movies/Edit/4` into a request to the `Edit` action method of the `Movies` controller with the parameter `Id` of 4.</span></span> <span data-ttu-id="3ceb4-121">(Les méthodes de contrôleur sont également appelées méthodes d’action.)</span><span class="sxs-lookup"><span data-stu-id="3ceb4-121">(Controller methods are also known as action methods.)</span></span>

<span data-ttu-id="3ceb4-122">Les [Tag Helpers](xref:mvc/views/tag-helpers/intro) sont l’une des nouvelles fonctionnalités les plus populaires dans ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-122">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are one of the most popular new features in ASP.NET Core.</span></span> <span data-ttu-id="3ceb4-123">Pour plus d'informations, consultez [Ressources supplémentaires](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="3ceb4-123">For more information, see [Additional resources](#additional-resources).</span></span>

<a name="get-post"></a>

<span data-ttu-id="3ceb4-124">Ouvrez le contrôleur `Movies` et examinez les deux méthodes d’action `Edit`.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-124">Open the `Movies` controller and examine the two `Edit` action methods.</span></span> <span data-ttu-id="3ceb4-125">Le code suivant montre la `HTTP GET Edit` méthode, qui extrait le film et remplit le formulaire de modification généré par le fichier *Edit. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="3ceb4-125">The following code shows the `HTTP GET Edit` method, which fetches the movie and populates the edit form generated by the *Edit.cshtml* Razor file.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="3ceb4-126">Le code suivant montre la méthode `HTTP POST Edit`, qui traite les valeurs de film publiées :</span><span class="sxs-lookup"><span data-stu-id="3ceb4-126">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="3ceb4-127">Le code suivant montre la méthode `HTTP POST Edit`, qui traite les valeurs de film publiées :</span><span class="sxs-lookup"><span data-stu-id="3ceb4-127">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="3ceb4-128">L’attribut `[Bind]` est l’un des moyens qui permettent d’assurer une protection contre la [sur-publication](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span><span class="sxs-lookup"><span data-stu-id="3ceb4-128">The `[Bind]` attribute is one way to protect against [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span></span> <span data-ttu-id="3ceb4-129">Vous devez inclure dans l’attribut `[Bind]` uniquement les propriétés que vous souhaitez modifier.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-129">You should only include properties in the `[Bind]` attribute that you want to change.</span></span> <span data-ttu-id="3ceb4-130">Pour plus d’informations, consultez [Protéger votre contrôleur contre la sur-publication](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="3ceb4-130">For more information, see [Protect your controller from over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span></span> <span data-ttu-id="3ceb4-131">Les [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) fournissent une alternative pour empêcher la sur-publication.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-131">[ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) provide an alternative approach to prevent over-posting.</span></span>

<span data-ttu-id="3ceb4-132">Notez que la deuxième méthode d’action `Edit` est précédée de l’attribut `[HttpPost]`.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-132">Notice the second `Edit` action method is preceded by the `[HttpPost]` attribute.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

<span data-ttu-id="3ceb4-133">L’attribut `HttpPost` indique que cette méthode `Edit` peut être appelée *uniquement* pour les requêtes `POST`.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-133">The `HttpPost` attribute specifies that this `Edit` method can be invoked *only* for `POST` requests.</span></span> <span data-ttu-id="3ceb4-134">Vous pouvez appliquer l’attribut `[HttpGet]` à la première méthode Edit, mais cela n’est pas nécessaire car `[HttpGet]` est la valeur par défaut.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-134">You could apply the `[HttpGet]` attribute to the first edit method, but that's not necessary because `[HttpGet]` is the default.</span></span>

<span data-ttu-id="3ceb4-135">L’attribut `ValidateAntiForgeryToken` est utilisé pour [lutter contre la falsification de requête](xref:security/anti-request-forgery). Il est associé à un jeton anti-contrefaçon généré dans le fichier de la vue Edit (*Views/Movies/Edit.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="3ceb4-135">The `ValidateAntiForgeryToken` attribute is used to [prevent forgery of a request](xref:security/anti-request-forgery) and is paired up with an anti-forgery token generated in the edit view file (*Views/Movies/Edit.cshtml*).</span></span> <span data-ttu-id="3ceb4-136">Le fichier de la vue Edit génère le jeton anti-contrefaçon avec le [Tag Helper Form](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="3ceb4-136">The edit view file generates the anti-forgery token with the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

<span data-ttu-id="3ceb4-137">Le [Tag Helper Form](xref:mvc/views/working-with-forms) génère un jeton anti-contrefaçon masqué qui doit correspondre au jeton anti-contrefaçon généré par `[ValidateAntiForgeryToken]` dans la méthode `Edit` du contrôleur Movies.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-137">The [Form Tag Helper](xref:mvc/views/working-with-forms) generates a hidden anti-forgery token that must match the `[ValidateAntiForgeryToken]` generated anti-forgery token in the `Edit` method of the Movies controller.</span></span> <span data-ttu-id="3ceb4-138">Pour plus d’informations, consultez <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-138">For more information, see <xref:security/anti-request-forgery>.</span></span>

<span data-ttu-id="3ceb4-139">La méthode `HttpGet Edit` prend le paramètre `ID` du film, recherche le film à l’aide de la méthode Entity Framework `FindAsync`, et retourne le film sélectionné à la vue Edit.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-139">The `HttpGet Edit` method takes the movie `ID` parameter, looks up the movie using the Entity Framework `FindAsync` method, and returns the selected movie to the Edit view.</span></span> <span data-ttu-id="3ceb4-140">Si un film est introuvable, l’erreur `NotFound` (HTTP 404) est retournée.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-140">If a movie cannot be found, `NotFound` (HTTP 404) is returned.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="3ceb4-141">Quand le système de génération de modèles automatique a créé la vue Edit, il a examiné la classe `Movie` et a créé le code pour restituer les éléments `<label>` et `<input>` de chaque propriété de la classe.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-141">When the scaffolding system created the Edit view, it examined the `Movie` class and created code to render `<label>` and `<input>` elements for each property of the class.</span></span> <span data-ttu-id="3ceb4-142">L’exemple suivant montre la vue Edit qui a été générée par le système de génération de modèles automatique de Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="3ceb4-142">The following example shows the Edit view that was generated by the Visual Studio scaffolding system:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

<span data-ttu-id="3ceb4-143">Notez que le modèle de vue comporte une instruction `@model MvcMovie.Models.Movie` en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-143">Notice how the view template has a `@model MvcMovie.Models.Movie` statement at the top of the file.</span></span> <span data-ttu-id="3ceb4-144">`@model MvcMovie.Models.Movie` indique que la vue s’attend à ce que le modèle pour le modèle de vue soit de type `Movie`.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-144">`@model MvcMovie.Models.Movie` specifies that the view expects the model for the view template to be of type `Movie`.</span></span>

<span data-ttu-id="3ceb4-145">Le code de génération de modèles automatique utilise plusieurs méthodes Tag Helper afin de rationaliser le balisage HTML.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-145">The scaffolded code uses several Tag Helper methods to streamline the HTML markup.</span></span> <span data-ttu-id="3ceb4-146">Le [tag Helper étiquette](xref:mvc/views/working-with-forms) affiche le nom du champ (« title », « Released », « genre » ou « Price »).</span><span class="sxs-lookup"><span data-stu-id="3ceb4-146">The [Label Tag Helper](xref:mvc/views/working-with-forms) displays the name of the field ("Title", "ReleaseDate", "Genre", or "Price").</span></span> <span data-ttu-id="3ceb4-147">Le [Tag Helper Input](xref:mvc/views/working-with-forms) affiche l’élément `<input>` HTML.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-147">The [Input Tag Helper](xref:mvc/views/working-with-forms) renders an HTML `<input>` element.</span></span> <span data-ttu-id="3ceb4-148">Le [Tag Helper Validation](xref:mvc/views/working-with-forms) affiche les messages de validation associés à cette propriété.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-148">The [Validation Tag Helper](xref:mvc/views/working-with-forms) displays any validation messages associated with that property.</span></span>

<span data-ttu-id="3ceb4-149">Exécutez l’application et accédez à l’URL `/Movies`.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-149">Run the application and navigate to the `/Movies` URL.</span></span> <span data-ttu-id="3ceb4-150">Cliquez sur un lien **modifier** .</span><span class="sxs-lookup"><span data-stu-id="3ceb4-150">Click an **Edit** link.</span></span> <span data-ttu-id="3ceb4-151">Dans le navigateur, affichez la source de la page.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-151">In the browser, view the source for the page.</span></span> <span data-ttu-id="3ceb4-152">Le code HTML généré pour l’élément `<form>` est indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-152">The generated HTML for the `<form>` element is shown below.</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

<span data-ttu-id="3ceb4-153">Les éléments `<input>` sont dans un élément `HTML <form>` dont l’attribut `action` est défini de façon à publier à l’URL `/Movies/Edit/id`.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-153">The `<input>` elements are in an `HTML <form>` element whose `action` attribute is set to post to the `/Movies/Edit/id` URL.</span></span> <span data-ttu-id="3ceb4-154">Les données du formulaire sont publiées au serveur en cas de clic sur le bouton `Save`.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-154">The form data will be posted to the server when the `Save` button is clicked.</span></span> <span data-ttu-id="3ceb4-155">La dernière ligne avant l’élément `</form>` de fermeture montre le jeton [XSRF](xref:security/anti-request-forgery) masqué généré par le [Tag Helper Form](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="3ceb4-155">The last line before the closing `</form>` element shows the hidden [XSRF](xref:security/anti-request-forgery) token generated by the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

## <a name="processing-the-post-request"></a><span data-ttu-id="3ceb4-156">Traitement de la requête POST</span><span class="sxs-lookup"><span data-stu-id="3ceb4-156">Processing the POST Request</span></span>

<span data-ttu-id="3ceb4-157">Le code suivant montre la version `[HttpPost]` de la méthode d’action `Edit`.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-157">The following listing shows the `[HttpPost]` version of the `Edit` action method.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="3ceb4-158">L’attribut `[ValidateAntiForgeryToken]` valide le jeton [XSRF](xref:security/anti-request-forgery) masqué généré par le générateur de jetons anti-contrefaçon dans le [Tag Helper Form](xref:mvc/views/working-with-forms)</span><span class="sxs-lookup"><span data-stu-id="3ceb4-158">The `[ValidateAntiForgeryToken]` attribute validates the hidden [XSRF](xref:security/anti-request-forgery) token generated by the anti-forgery token generator in the [Form Tag Helper](xref:mvc/views/working-with-forms)</span></span>

<span data-ttu-id="3ceb4-159">Le système de [liaison de modèle](xref:mvc/models/model-binding) prend les valeurs de formulaire publiées et crée un objet `Movie` qui est passé en tant que paramètre `movie`.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-159">The [model binding](xref:mvc/models/model-binding) system takes the posted form values and creates a `Movie` object that's passed as the `movie` parameter.</span></span> <span data-ttu-id="3ceb4-160">La `ModelState.IsValid` propriété vérifie que les données envoyées dans le formulaire peuvent être utilisées pour modifier (modifier ou mettre à jour) un `Movie` objet.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-160">The `ModelState.IsValid` property verifies that the data submitted in the form can be used to modify (edit or update) a `Movie` object.</span></span> <span data-ttu-id="3ceb4-161">Si les données sont valides, elles sont enregistrées.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-161">If the data is valid, it's saved.</span></span> <span data-ttu-id="3ceb4-162">Les données de film mises à jour (modifiées) sont enregistrées dans la base de données en appelant la méthode `SaveChangesAsync` du contexte de base de données.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-162">The updated (edited) movie data is saved to the database by calling the `SaveChangesAsync` method of database context.</span></span> <span data-ttu-id="3ceb4-163">Après avoir enregistré les données, le code redirige l’utilisateur vers la méthode d’action `Index` de la classe `MoviesController`, qui affiche la collection de films, avec notamment les modifications qui viennent d’être apportées.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-163">After saving the data, the code redirects the user to the `Index` action method of the `MoviesController` class, which displays the movie collection, including the changes just made.</span></span>

<span data-ttu-id="3ceb4-164">Avant que le formulaire soit publié sur le serveur, la validation côté client vérifie les règles de validation sur les champs.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-164">Before the form is posted to the server, client-side validation checks any validation rules on the fields.</span></span> <span data-ttu-id="3ceb4-165">En cas d’erreur de validation, un message d’erreur s’affiche et le formulaire n’est pas publié.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-165">If there are any validation errors, an error message is displayed and the form isn't posted.</span></span> <span data-ttu-id="3ceb4-166">Si JavaScript est désactivé, aucune validation côté client n’est effectuée, mais le serveur détecte les valeurs publiées qui ne sont pas valides, et les valeurs de formulaire sont réaffichées avec des messages d’erreur.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-166">If JavaScript is disabled, you won't have client-side validation but the server will detect the posted values that are not valid, and the form values will be redisplayed with error messages.</span></span> <span data-ttu-id="3ceb4-167">Plus loin dans ce didacticiel, nous examinerons la [Validation du modèle](xref:mvc/models/validation) plus en détail.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-167">Later in the tutorial we examine [Model Validation](xref:mvc/models/validation) in more detail.</span></span> <span data-ttu-id="3ceb4-168">Le [Tag Helper Validation](xref:mvc/views/working-with-forms) dans le modèle de vue *Views/Movies/Edit.cshtml* se charge de l’affichage des messages d’erreur appropriés.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-168">The [Validation Tag Helper](xref:mvc/views/working-with-forms) in the *Views/Movies/Edit.cshtml* view template takes care of displaying appropriate error messages.</span></span>

![Vue Edit : une exception pour une valeur Price incorrecte « abc » indique que le champ Price doit être un nombre.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

<span data-ttu-id="3ceb4-171">Toutes les méthodes `HttpGet` du contrôleur Movies suivent un modèle similaire.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-171">All the `HttpGet` methods in the movie controller follow a similar pattern.</span></span> <span data-ttu-id="3ceb4-172">Elles reçoivent un objet de film (ou une liste d’objets, dans le cas de `Index`) et passent l’objet (modèle) à la vue.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-172">They get a movie object (or list of objects, in the case of `Index`), and pass the object (model) to the view.</span></span> <span data-ttu-id="3ceb4-173">La méthode `Create` passe un objet de film vide à la vue `Create`.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-173">The `Create` method passes an empty movie object to the `Create` view.</span></span> <span data-ttu-id="3ceb4-174">Toutes les méthodes qui créent, modifient, suppriment ou changent d’une quelconque manière des données le font dans la surcharge `[HttpPost]` de la méthode.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-174">All the methods that create, edit, delete, or otherwise modify data do so in the `[HttpPost]` overload of the method.</span></span> <span data-ttu-id="3ceb4-175">Modifier des données dans une méthode `HTTP GET` présente un risque pour la sécurité.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-175">Modifying data in an `HTTP GET` method is a security risk.</span></span> <span data-ttu-id="3ceb4-176">La modification des données dans une méthode `HTTP GET` enfreint également les bonnes pratiques HTTP et le modèle architectural [REST](http://rest.elkstein.org/), qui spécifie que les requêtes GET ne doivent pas changer l’état de votre application.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-176">Modifying data in an `HTTP GET` method also violates HTTP best practices and the architectural [REST](http://rest.elkstein.org/) pattern, which specifies that GET requests shouldn't change the state of your application.</span></span> <span data-ttu-id="3ceb4-177">En d’autres termes, une opération GET doit être sûre, ne doit avoir aucun effet secondaire et ne doit pas modifier vos données persistantes.</span><span class="sxs-lookup"><span data-stu-id="3ceb4-177">In other words, performing a GET operation should be a safe operation that has no side effects and doesn't modify your persisted data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3ceb4-178">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="3ceb4-178">Additional resources</span></span>

* [<span data-ttu-id="3ceb4-179">Globalisation et localisation</span><span class="sxs-lookup"><span data-stu-id="3ceb4-179">Globalization and localization</span></span>](xref:fundamentals/localization)
* [<span data-ttu-id="3ceb4-180">Introduction aux Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="3ceb4-180">Introduction to Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="3ceb4-181">Créer des Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="3ceb4-181">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* <span data-ttu-id="3ceb4-182">Protéger votre contrôleur contre la [sur-publication](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span><span class="sxs-lookup"><span data-stu-id="3ceb4-182">Protect your controller from [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span></span>
* [<span data-ttu-id="3ceb4-183">ViewModels</span><span class="sxs-lookup"><span data-stu-id="3ceb4-183">ViewModels</span></span>](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [<span data-ttu-id="3ceb4-184">Tag Helper Form</span><span class="sxs-lookup"><span data-stu-id="3ceb4-184">Form Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="3ceb4-185">Tag Helper Input</span><span class="sxs-lookup"><span data-stu-id="3ceb4-185">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="3ceb4-186">Tag Helper Label</span><span class="sxs-lookup"><span data-stu-id="3ceb4-186">Label Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="3ceb4-187">Tag Helper Select</span><span class="sxs-lookup"><span data-stu-id="3ceb4-187">Select Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="3ceb4-188">Tag Helper Validation</span><span class="sxs-lookup"><span data-stu-id="3ceb4-188">Validation Tag Helper</span></span>](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> <span data-ttu-id="3ceb4-189">[Précédent](working-with-sql.md) 
>  [Suivant](search.md)</span><span class="sxs-lookup"><span data-stu-id="3ceb4-189">[Previous](working-with-sql.md)
[Next](search.md)</span></span>  
