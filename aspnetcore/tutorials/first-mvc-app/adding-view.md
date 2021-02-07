---
title: 'Partie 3 : ajouter une vue à une application ASP.NET Core MVC'
author: rick-anderson
description: Partie 3 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.date: 01/28/2021
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
uid: tutorials/first-mvc-app/adding-view
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804567"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="1e47a-103">Partie 3 : ajouter une vue à une application ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="1e47a-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="1e47a-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1e47a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e47a-105">Dans cette section, vous allez modifier la `HelloWorldController` classe pour utiliser des [Razor](xref:mvc/views/razor) fichiers de vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-105">In this section, you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files.</span></span> <span data-ttu-id="1e47a-106">Cela encapsule correctement le processus de génération de réponses HTML sur un client.</span><span class="sxs-lookup"><span data-stu-id="1e47a-106">This cleanly encapsulates the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="1e47a-107">Les modèles de vue sont créés à l’aide de Razor .</span><span class="sxs-lookup"><span data-stu-id="1e47a-107">View templates are created using Razor.</span></span> <span data-ttu-id="1e47a-108">Razormodèles de vue basés sur :</span><span class="sxs-lookup"><span data-stu-id="1e47a-108">Razor-based view templates:</span></span>

* <span data-ttu-id="1e47a-109">Avoir une extension de fichier *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1e47a-109">Have a *.cshtml* file extension.</span></span>
* <span data-ttu-id="1e47a-110">Fournissez un moyen élégant de créer une sortie HTML avec C#.</span><span class="sxs-lookup"><span data-stu-id="1e47a-110">Provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="1e47a-111">Actuellement `Index` , la méthode retourne une chaîne avec un message dans la classe Controller.</span><span class="sxs-lookup"><span data-stu-id="1e47a-111">Currently the `Index` method returns a string with a message in the controller class.</span></span> <span data-ttu-id="1e47a-112">Dans la classe `HelloWorldController`, remplacez la méthode `Index` par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="1e47a-112">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="1e47a-113">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="1e47a-113">The preceding code:</span></span>

* <span data-ttu-id="1e47a-114">Appelle la méthode du contrôleur <xref:Microsoft.AspNetCore.Mvc.Controller.View*> .</span><span class="sxs-lookup"><span data-stu-id="1e47a-114">Calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span>
* <span data-ttu-id="1e47a-115">Utilise un modèle de vue pour générer une réponse HTML.</span><span class="sxs-lookup"><span data-stu-id="1e47a-115">Uses a view template to generate an HTML response.</span></span>

<span data-ttu-id="1e47a-116">Méthodes de contrôleur :</span><span class="sxs-lookup"><span data-stu-id="1e47a-116">Controller methods:</span></span>

* <span data-ttu-id="1e47a-117">Sont appelées *méthodes d’action*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-117">Are referred to as *action methods*.</span></span>  <span data-ttu-id="1e47a-118">Par exemple, la `Index` méthode d’action dans le code précédent.</span><span class="sxs-lookup"><span data-stu-id="1e47a-118">For example, the `Index` action method in the preceding code.</span></span>
* <span data-ttu-id="1e47a-119">Retourne généralement un <xref:Microsoft.AspNetCore.Mvc.IActionResult> ou une classe dérivée de <xref:Microsoft.AspNetCore.Mvc.ActionResult> , et non un type comme `string` .</span><span class="sxs-lookup"><span data-stu-id="1e47a-119">Generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>, not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="1e47a-120">Ajouter une vue</span><span class="sxs-lookup"><span data-stu-id="1e47a-120">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e47a-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e47a-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1e47a-122">Cliquez avec le bouton droit sur le dossier *views* , puis **Ajoutez > nouveau dossier** et nommez le dossier *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-122">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="1e47a-123">Cliquez avec le bouton droit sur le dossier *views/HelloWorld* , puis **Ajoutez > nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-123">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

<span data-ttu-id="1e47a-124">Dans la boîte de dialogue **Ajouter un nouvel élément-MvcMovie** :</span><span class="sxs-lookup"><span data-stu-id="1e47a-124">In the **Add New Item - MvcMovie** dialog:</span></span>

* <span data-ttu-id="1e47a-125">Dans la zone de recherche située en haut à droite, entrez *vue*</span><span class="sxs-lookup"><span data-stu-id="1e47a-125">In the search box in the upper-right, enter *view*</span></span>
* <span data-ttu-id="1e47a-126">Sélectionner une **Razor vue**</span><span class="sxs-lookup"><span data-stu-id="1e47a-126">Select **Razor View**</span></span>
* <span data-ttu-id="1e47a-127">Conservez la valeur de la zone **Nom**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-127">Keep the **Name** box value, *Index.cshtml*.</span></span>
* <span data-ttu-id="1e47a-128">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="1e47a-128">Select **Add**</span></span>

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e47a-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e47a-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1e47a-131">Ajoutez une `Index` vue pour le `HelloWorldController` :</span><span class="sxs-lookup"><span data-stu-id="1e47a-131">Add an `Index` view for the `HelloWorldController`:</span></span>

* <span data-ttu-id="1e47a-132">Ajoutez un nouveau dossier nommé *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-132">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="1e47a-133">Ajoutez un nouveau fichier à la *vues/HelloWorld* nom du dossier *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-133">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e47a-134">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="1e47a-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1e47a-135">Cliquez avec le contrôle sur le dossier *views* , puis **Ajoutez > nouveau dossier** et nommez le dossier *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-135">Control-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="1e47a-136">Cliquez avec le contrôle sur le dossier *views/HelloWorld* , puis **Ajoutez > nouveau fichier**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-136">Control-click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>

<span data-ttu-id="1e47a-137">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="1e47a-137">In the **New File** dialog:</span></span>

* <span data-ttu-id="1e47a-138">Dans le volet gauche, sélectionnez **ASP.net Core** .</span><span class="sxs-lookup"><span data-stu-id="1e47a-138">Select **ASP.NET Core** in the left pane.</span></span>
* <span data-ttu-id="1e47a-139">Sélectionnez **Razor affichage-vide** dans le volet central.</span><span class="sxs-lookup"><span data-stu-id="1e47a-139">Select **Razor View - Empty** in the center pane.</span></span>
* <span data-ttu-id="1e47a-140">Dans la zone **nom** , tapez *index* .</span><span class="sxs-lookup"><span data-stu-id="1e47a-140">Type *Index* in the **Name** box.</span></span>
* <span data-ttu-id="1e47a-141">Sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-141">Select **New**.</span></span>

  ![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="1e47a-143">Remplacez le contenu du fichier de vue *views/HelloWorld/index. cshtml* Razor par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="1e47a-143">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="1e47a-144">Accédez à `https://localhost:{PORT}/HelloWorld` :</span><span class="sxs-lookup"><span data-stu-id="1e47a-144">Navigate to `https://localhost:{PORT}/HelloWorld`:</span></span>

* <span data-ttu-id="1e47a-145">La `Index` méthode dans le a `HelloWorldController` exécuté l’instruction `return View();` , qui spécifie que la méthode doit utiliser un fichier de modèle de vue pour afficher une réponse dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-145">The `Index` method in the `HelloWorldController` ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span>
* <span data-ttu-id="1e47a-146">Comme un nom de fichier de modèle de vue n’A pas été spécifié, MVC utilise par défaut le fichier de vue par défaut.</span><span class="sxs-lookup"><span data-stu-id="1e47a-146">A view template file name wasn't specified, so MVC defaulted to using the default view file.</span></span> <span data-ttu-id="1e47a-147">Lorsque le nom du fichier de vue n’est pas spécifié, la vue par défaut est retournée.</span><span class="sxs-lookup"><span data-stu-id="1e47a-147">When the view file name isn't specified, the default view is returned.</span></span> <span data-ttu-id="1e47a-148">L’affichage par défaut porte le même nom que la méthode d’action, `Index` dans cet exemple.</span><span class="sxs-lookup"><span data-stu-id="1e47a-148">The default view has the same name as the action method, `Index` in this example.</span></span> <span data-ttu-id="1e47a-149">Le modèle de vue */views/HelloWorld/index.cshtml* est utilisé.</span><span class="sxs-lookup"><span data-stu-id="1e47a-149">The view template */Views/HelloWorld/Index.cshtml* is used.</span></span>
* <span data-ttu-id="1e47a-150">L’image suivante montre la chaîne « Hello from the View template ! »</span><span class="sxs-lookup"><span data-stu-id="1e47a-150">The following image shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="1e47a-151">codé en dur dans la vue :</span><span class="sxs-lookup"><span data-stu-id="1e47a-151">hard-coded in the view:</span></span>

  ![Fenêtre du navigateur](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="1e47a-153">Changer les vues et les pages de disposition</span><span class="sxs-lookup"><span data-stu-id="1e47a-153">Change views and layout pages</span></span>

<span data-ttu-id="1e47a-154">Sélectionnez le menu liens **MvcMovie**, **orig** et **Privacy**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-154">Select the menu links **MvcMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="1e47a-155">Chaque page affiche la même disposition de menu.</span><span class="sxs-lookup"><span data-stu-id="1e47a-155">Each page shows the same menu layout.</span></span> <span data-ttu-id="1e47a-156">La disposition du menu est implémentée dans le fichier *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-156">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1e47a-157">Ouvrez le fichier *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-157">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1e47a-158">Les modèles de [disposition](xref:mvc/views/layout) permettent :</span><span class="sxs-lookup"><span data-stu-id="1e47a-158">[Layout](xref:mvc/views/layout) templates allows:</span></span>

* <span data-ttu-id="1e47a-159">Spécification de la disposition de conteneur HTML d’un site dans un emplacement unique.</span><span class="sxs-lookup"><span data-stu-id="1e47a-159">Specifying the HTML container layout of a site in one place.</span></span>
* <span data-ttu-id="1e47a-160">Application de la disposition de conteneur HTML sur plusieurs pages du site.</span><span class="sxs-lookup"><span data-stu-id="1e47a-160">Applying the HTML container layout across multiple pages in the site.</span></span>

<span data-ttu-id="1e47a-161">Recherchez la ligne `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-161">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="1e47a-162">`RenderBody` est un espace réservé dans lequel toutes les pages spécifiques aux vues que vous créez s’affichent, *encapsulées* dans la page de disposition.</span><span class="sxs-lookup"><span data-stu-id="1e47a-162">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="1e47a-163">Par exemple, si vous sélectionnez le lien **Confidentialité**, la vue **Views/Home/Privacy.cshtml** est restituée dans la méthode `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-163">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="1e47a-164">Changer le lien de titre, de pied de page et de menu dans le fichier de disposition</span><span class="sxs-lookup"><span data-stu-id="1e47a-164">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="1e47a-165">Remplacez le contenu du fichier *Views/Shared/_Layout. cshtml* par le balisage suivant.</span><span class="sxs-lookup"><span data-stu-id="1e47a-165">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="1e47a-166">Les modifications apparaissent en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="1e47a-166">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e47a-167">Le balisage précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="1e47a-167">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="1e47a-168">Trois occurrences de `MvcMovie` à `Movie App` .</span><span class="sxs-lookup"><span data-stu-id="1e47a-168">Three occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="1e47a-169">Remplacement de l’élément d’ancrage `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` par `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-169">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="1e47a-170">Dans le balisage précédent, l' `asp-area=""` attribut et la valeur [d’attribut d’ancre de balise d’ancrage](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) ont été omis, car cette application n’utilise pas de [zones](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="1e47a-170">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app isn't using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="1e47a-171">**Remarque**: le `Movies` contrôleur n’a pas été implémenté.</span><span class="sxs-lookup"><span data-stu-id="1e47a-171">**Note**: The `Movies` controller hasn't been implemented.</span></span> <span data-ttu-id="1e47a-172">À ce stade, le `Movie App` lien n’est pas fonctionnel.</span><span class="sxs-lookup"><span data-stu-id="1e47a-172">At this point, the `Movie App` link isn't functional.</span></span>

<span data-ttu-id="1e47a-173">Enregistrez les modifications et sélectionnez le lien **confidentialité** .</span><span class="sxs-lookup"><span data-stu-id="1e47a-173">Save the changes and select the **Privacy** link.</span></span> <span data-ttu-id="1e47a-174">Notez comment le titre sur l’onglet du navigateur affiche **Stratégie de confidentialité - Movie App** au lieu de **Stratégie de confidentialité - Mvc Movie** :</span><span class="sxs-lookup"><span data-stu-id="1e47a-174">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Onglet Confidentialité](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="1e47a-176">Sélectionnez le lien **Home** (Accueil).</span><span class="sxs-lookup"><span data-stu-id="1e47a-176">Select the **Home** link.</span></span>

<span data-ttu-id="1e47a-177">Notez que le titre et le texte d’ancrage affichent l' **application vidéo**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-177">Notice that the title and anchor text display **Movie App**.</span></span> <span data-ttu-id="1e47a-178">Les modifications ont été apportées une fois dans le modèle de disposition et toutes les pages du site reflètent le nouveau texte de lien et le nouveau titre.</span><span class="sxs-lookup"><span data-stu-id="1e47a-178">The changes were made once in the layout template and all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="1e47a-179">Examinez le fichier *Views/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1e47a-179">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="1e47a-180">Le fichier *Views/_ViewStart.cshtml* introduit le fichier *Views/Shared/_Layout.cshtml* dans chaque vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-180">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="1e47a-181">La propriété `Layout` peut être utilisée pour définir un mode de disposition différent ou lui affecter la valeur `null`. Aucun fichier de disposition n’est donc utilisé.</span><span class="sxs-lookup"><span data-stu-id="1e47a-181">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="1e47a-182">Ouvrez le fichier de vue *views/HelloWorld/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1e47a-182">Open the *Views/HelloWorld/Index.cshtml* view file.</span></span>

<span data-ttu-id="1e47a-183">Modifiez le titre et l' `<h2>` élément comme indiqué dans les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="1e47a-183">Change the title and `<h2>` element as highlighted in the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="1e47a-184">Le titre et l' `<h2>` élément sont légèrement différents. il est donc clair qu’une partie du code modifie l’affichage.</span><span class="sxs-lookup"><span data-stu-id="1e47a-184">The title and `<h2>` element are slightly different so it's clear which part of the code changes the display.</span></span>

<span data-ttu-id="1e47a-185">Dans le code ci-dessus, `ViewData["Title"] = "Movie List";` définit la propriété `Title` du dictionnaire `ViewData` sur « Movie List ».</span><span class="sxs-lookup"><span data-stu-id="1e47a-185">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="1e47a-186">La propriété `Title` est utilisée dans l’élément HTML `<title>` dans la page de disposition :</span><span class="sxs-lookup"><span data-stu-id="1e47a-186">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="1e47a-187">Enregistrez la modification et accédez à `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-187">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span>

<span data-ttu-id="1e47a-188">Notez que les éléments suivants ont été modifiés :</span><span class="sxs-lookup"><span data-stu-id="1e47a-188">Notice that the following have changed:</span></span>

* <span data-ttu-id="1e47a-189">Titre du navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-189">Browser title.</span></span>
* <span data-ttu-id="1e47a-190">Titre principal.</span><span class="sxs-lookup"><span data-stu-id="1e47a-190">Primary heading.</span></span>
* <span data-ttu-id="1e47a-191">Titres secondaires.</span><span class="sxs-lookup"><span data-stu-id="1e47a-191">Secondary headings.</span></span>

<span data-ttu-id="1e47a-192">S’il n’y a aucune modification dans le navigateur, il peut s’agir d’un contenu mis en cache affiché.</span><span class="sxs-lookup"><span data-stu-id="1e47a-192">If there are no changes in the browser, it could be cached content that is being viewed.</span></span> <span data-ttu-id="1e47a-193">Appuyez sur CTRL + F5 dans le navigateur pour forcer le chargement de la réponse du serveur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-193">Press Ctrl+F5 in the browser to force the response from the server to be loaded.</span></span> <span data-ttu-id="1e47a-194">Le titre du navigateur est créé avec la valeur `ViewData["Title"]` que nous avons définie dans le modèle de vue *Index.cshtml* et la chaîne « - Movie App » ajoutée dans le fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="1e47a-194">The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="1e47a-195">Le contenu du modèle de vue *Index.cshtml* est fusionné avec le modèle de vue *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-195">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="1e47a-196">Une réponse HTML unique est envoyée au navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-196">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="1e47a-197">Les modèles de disposition permettent d’apporter facilement des modifications qui s’appliquent à toutes les pages d’une application.</span><span class="sxs-lookup"><span data-stu-id="1e47a-197">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="1e47a-198">Pour en savoir plus, consultez [Disposition](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="1e47a-198">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Vue Movie List](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

<span data-ttu-id="1e47a-200">Le petit peu de « données », le « Bonjour de notre modèle de vue ! »</span><span class="sxs-lookup"><span data-stu-id="1e47a-200">The small bit of "data", the "Hello from our View Template!"</span></span> <span data-ttu-id="1e47a-201">message, est toutefois codé en dur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-201">message, is hard-coded however.</span></span> <span data-ttu-id="1e47a-202">L’application MVC a un « V » (affichage), un « C » (contrôleur), mais pas encore de « M » (modèle).</span><span class="sxs-lookup"><span data-stu-id="1e47a-202">The MVC application has a "V" (view), a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="1e47a-203">Passage de données du contrôleur vers la vue</span><span class="sxs-lookup"><span data-stu-id="1e47a-203">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="1e47a-204">Les actions du contrôleur sont appelées en réponse à une demande d’URL entrante.</span><span class="sxs-lookup"><span data-stu-id="1e47a-204">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="1e47a-205">Une classe de contrôleur est l’endroit où le code est écrit et qui gère les demandes du navigateur entrantes.</span><span class="sxs-lookup"><span data-stu-id="1e47a-205">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="1e47a-206">Le contrôleur récupère les données d’une source de données et détermine le type de réponse à envoyer au navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-206">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="1e47a-207">Il est possible d’utiliser des modèles de vue à partir d’un contrôleur pour générer et mettre en forme une réponse HTML au navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-207">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="1e47a-208">Les contrôleurs sont chargés de fournir les données nécessaires pour qu’un modèle de vue restitue une réponse.</span><span class="sxs-lookup"><span data-stu-id="1e47a-208">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span>

<span data-ttu-id="1e47a-209">Les modèles de vue ne doivent **pas**:</span><span class="sxs-lookup"><span data-stu-id="1e47a-209">View templates should **not**:</span></span>

* <span data-ttu-id="1e47a-210">Logique métier</span><span class="sxs-lookup"><span data-stu-id="1e47a-210">Do business logic</span></span>
* <span data-ttu-id="1e47a-211">Interagir directement avec une base de données.</span><span class="sxs-lookup"><span data-stu-id="1e47a-211">Interact with a database directly.</span></span>

<span data-ttu-id="1e47a-212">Un modèle de vue doit fonctionner uniquement avec les données qui lui sont fournies par le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-212">A view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="1e47a-213">Le maintien de cette « séparation des préoccupations » vous aide à conserver le code :</span><span class="sxs-lookup"><span data-stu-id="1e47a-213">Maintaining this "separation of concerns" helps keep the code:</span></span>

* <span data-ttu-id="1e47a-214">Claire.</span><span class="sxs-lookup"><span data-stu-id="1e47a-214">Clean.</span></span>
* <span data-ttu-id="1e47a-215">Susceptible.</span><span class="sxs-lookup"><span data-stu-id="1e47a-215">Testable.</span></span>
* <span data-ttu-id="1e47a-216">Facile à gérer.</span><span class="sxs-lookup"><span data-stu-id="1e47a-216">Maintainable.</span></span>

<span data-ttu-id="1e47a-217">Actuellement, le `Welcome` méthode présente dans la classe `HelloWorldController` prend un paramètre `name` et un paramètre `ID`, puis sort les valeurs directement dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-217">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span>

<span data-ttu-id="1e47a-218">Au lieu que le contrôleur restitue cette réponse sous forme de chaîne, changez le contrôleur pour qu’il utilise un modèle de vue à la place.</span><span class="sxs-lookup"><span data-stu-id="1e47a-218">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="1e47a-219">Le modèle de vue génère une réponse dynamique, ce qui signifie que les données appropriées doivent être transmises du contrôleur à la vue pour générer la réponse.</span><span class="sxs-lookup"><span data-stu-id="1e47a-219">The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response.</span></span> <span data-ttu-id="1e47a-220">Pour ce faire, le contrôleur doit placer les données dynamiques (paramètres) dont le modèle de vue a besoin dans un `ViewData` dictionnaire.</span><span class="sxs-lookup"><span data-stu-id="1e47a-220">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary.</span></span> <span data-ttu-id="1e47a-221">Le modèle de vue peut ensuite accéder aux données dynamiques.</span><span class="sxs-lookup"><span data-stu-id="1e47a-221">The view template can then access the dynamic data.</span></span>

<span data-ttu-id="1e47a-222">Dans *HelloWorldController.cs*, modifiez la méthode `Welcome` pour ajouter une valeur `Message` et `NumTimes` au dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-222">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span>

<span data-ttu-id="1e47a-223">Le `ViewData` dictionnaire est un objet dynamique, ce qui signifie que tout type peut être utilisé.</span><span class="sxs-lookup"><span data-stu-id="1e47a-223">The `ViewData` dictionary is a dynamic object, which means any type can be used.</span></span> <span data-ttu-id="1e47a-224">L' `ViewData` objet n’a pas de propriétés définies tant qu’un objet n’est pas ajouté.</span><span class="sxs-lookup"><span data-stu-id="1e47a-224">The `ViewData` object has no defined properties until something is added.</span></span> <span data-ttu-id="1e47a-225">Le [système de liaison de modèle MVC](xref:mvc/models/model-binding) mappe automatiquement les paramètres nommés `name` et `numTimes` de la chaîne de requête aux paramètres de la méthode.</span><span class="sxs-lookup"><span data-stu-id="1e47a-225">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters `name` and `numTimes` from the query string to parameters in the method.</span></span> <span data-ttu-id="1e47a-226">L’ensemble complet `HelloWorldController` :</span><span class="sxs-lookup"><span data-stu-id="1e47a-226">The complete `HelloWorldController`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

<span data-ttu-id="1e47a-227">L’objet dictionnaire `ViewData` contient des données qui seront passées à la vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-227">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="1e47a-228">Créez un modèle de vue Welcome nommé *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-228">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="1e47a-229">Vous allez créer une boucle dans le modèle de vue *Welcome.cshtml* qui affiche « Hello » `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-229">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="1e47a-230">Remplacez le contenu de *Views/HelloWorld/Welcome.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="1e47a-230">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="1e47a-231">Enregistrez vos modifications et accédez à l’URL suivante :</span><span class="sxs-lookup"><span data-stu-id="1e47a-231">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="1e47a-232">Les données sont extraites de l’URL et transmises au contrôleur à l’aide du [Binder de modèle MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1e47a-232">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="1e47a-233">Le contrôleur empaquette les données dans un dictionnaire `ViewData` et passe cet objet à la vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-233">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="1e47a-234">La vue restitue ensuite les données au format HTML dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-234">The view then renders the data as HTML to the browser.</span></span>

![Vue Confidentialité affichant une étiquette Welcome et l’expression « Hello Rick » affichée quatre fois](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

<span data-ttu-id="1e47a-236">Dans l’exemple précédent, le `ViewData` dictionnaire a été utilisé pour passer des données du contrôleur à une vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-236">In the preceding sample, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="1e47a-237">Plus loin dans ce didacticiel, un modèle de vue est utilisé pour passer les données d’un contrôleur à une vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-237">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="1e47a-238">L’approche du modèle de vue pour passer des données est préférable à l' `ViewData` approche de dictionnaire.</span><span class="sxs-lookup"><span data-stu-id="1e47a-238">The view model approach to passing data is preferred over the `ViewData` dictionary approach.</span></span>

<span data-ttu-id="1e47a-239">Dans le didacticiel suivant, une base de données de films est créée.</span><span class="sxs-lookup"><span data-stu-id="1e47a-239">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1e47a-240">[Précédent : ajouter un contrôleur](adding-controller.md) 
>  [Suivant : ajouter un modèle](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="1e47a-240">[Previous: Add a Controller](adding-controller.md)
[Next: Add a Model](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e47a-241">Dans cette section, la `HelloWorldController` classe est modifiée de façon à utiliser [Razor](xref:mvc/views/razor) des fichiers de vue pour encapsuler correctement le processus de génération de réponses html sur un client.</span><span class="sxs-lookup"><span data-stu-id="1e47a-241">In this section, the `HelloWorldController` class is modified to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="1e47a-242">Vous créez un fichier de modèle de vue à l’aide de Razor .</span><span class="sxs-lookup"><span data-stu-id="1e47a-242">You create a view template file using Razor.</span></span> <span data-ttu-id="1e47a-243">Razorles modèles de vue basés sur utilisent une extension de fichier *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="1e47a-243">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="1e47a-244">Ils offrent un moyen élégant pour créer une sortie HTML avec C#.</span><span class="sxs-lookup"><span data-stu-id="1e47a-244">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="1e47a-245">Actuellement, la méthode `Index` retourne une chaîne avec un message qui est codé en dur dans la classe du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-245">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="1e47a-246">Dans la classe `HelloWorldController`, remplacez la méthode `Index` par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="1e47a-246">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="1e47a-247">Le code précédent appelle la méthode <xref:Microsoft.AspNetCore.Mvc.Controller.View*> du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-247">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="1e47a-248">Il utilise un modèle de vue pour générer une réponse HTML.</span><span class="sxs-lookup"><span data-stu-id="1e47a-248">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="1e47a-249">Les méthodes du contrôleur (également appelées *méthodes d’action*), comme la méthode `Index` ci-dessus, retournent généralement un <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou une classe dérivée de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), et non un type comme `string`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-249">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="1e47a-250">Ajouter une vue</span><span class="sxs-lookup"><span data-stu-id="1e47a-250">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1e47a-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1e47a-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1e47a-252">Cliquez avec le bouton droit sur le dossier *views* , puis **Ajoutez > nouveau dossier** et nommez le dossier *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-252">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="1e47a-253">Cliquez avec le bouton droit sur le dossier *views/HelloWorld* , puis **Ajoutez > nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-253">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="1e47a-254">Dans la boîte de dialogue **Ajouter un nouvel élément - MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="1e47a-254">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="1e47a-255">Dans la zone de recherche située en haut à droite, entrez *vue*</span><span class="sxs-lookup"><span data-stu-id="1e47a-255">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="1e47a-256">Sélectionner une **Razor vue**</span><span class="sxs-lookup"><span data-stu-id="1e47a-256">Select **Razor View**</span></span>

  * <span data-ttu-id="1e47a-257">Conservez la valeur de la zone **Nom**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-257">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="1e47a-258">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="1e47a-258">Select **Add**</span></span>

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="1e47a-260">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1e47a-260">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1e47a-261">Ajoutez une vue `Index` pour `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-261">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="1e47a-262">Ajoutez un nouveau dossier nommé *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-262">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="1e47a-263">Ajoutez un nouveau fichier à la *vues/HelloWorld* nom du dossier *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-263">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1e47a-264">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="1e47a-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1e47a-265">Cliquez avec le bouton droit sur le dossier *Vues*, cliquez sur **Ajouter > Nouveau dossier**, puis nommez le dossier *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-265">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="1e47a-266">Cliquez avec le bouton droit sur le dossier *Vues/HelloWorld*, puis cliquez sur **Ajouter > Nouveau fichier**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-266">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="1e47a-267">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="1e47a-267">In the **New File** dialog:</span></span>

  * <span data-ttu-id="1e47a-268">Sélectionnez **Web** dans le volet gauche.</span><span class="sxs-lookup"><span data-stu-id="1e47a-268">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="1e47a-269">Sélectionnez **Fichier HTML vide** dans le volet central.</span><span class="sxs-lookup"><span data-stu-id="1e47a-269">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="1e47a-270">Tapez *Index.cshtml* dans la zone **Nom**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-270">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="1e47a-271">Sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-271">Select **New**.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="1e47a-273">Remplacez le contenu du fichier de vue *views/HelloWorld/index. cshtml* Razor par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="1e47a-273">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="1e47a-274">Accédez à `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-274">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="1e47a-275">La méthode `Index` dans `HelloWorldController` n’a pas accompli beaucoup d’actions. Elle a exécuté l’instruction `return View();`, laquelle spécifiait que la méthode doit utiliser un fichier de modèle de vue pour restituer une réponse au navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-275">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="1e47a-276">Comme aucun nom de fichier de modèle de vue n’a été spécifié, MVC utilise le fichier d’affichage par défaut.</span><span class="sxs-lookup"><span data-stu-id="1e47a-276">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="1e47a-277">Le fichier d’affichage par défaut a le même nom que la méthode (`Index`), donc */Views/HelloWorld/Index.cshtml* est utilisé.</span><span class="sxs-lookup"><span data-stu-id="1e47a-277">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="1e47a-278">L’image ci-dessous montre la chaîne « Hello from our View Template! »</span><span class="sxs-lookup"><span data-stu-id="1e47a-278">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="1e47a-279">codée en dur dans la vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-279">hard-coded in the view.</span></span>

![Fenêtre du navigateur](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="1e47a-281">Changer les vues et les pages de disposition</span><span class="sxs-lookup"><span data-stu-id="1e47a-281">Change views and layout pages</span></span>

<span data-ttu-id="1e47a-282">Sélectionnez les liens du menu (**MvcMovie**, **Accueil** et **Confidentialité**).</span><span class="sxs-lookup"><span data-stu-id="1e47a-282">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="1e47a-283">Chaque page affiche la même disposition de menu.</span><span class="sxs-lookup"><span data-stu-id="1e47a-283">Each page shows the same menu layout.</span></span> <span data-ttu-id="1e47a-284">La disposition du menu est implémentée dans le fichier *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-284">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="1e47a-285">Ouvrez le fichier *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-285">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="1e47a-286">Les modèles de [disposition](xref:mvc/views/layout) vous permettent de spécifier la disposition du conteneur HTML de votre site dans un emplacement unique, puis de l’appliquer sur plusieurs pages de votre site.</span><span class="sxs-lookup"><span data-stu-id="1e47a-286">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="1e47a-287">Recherchez la ligne `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-287">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="1e47a-288">`RenderBody` est un espace réservé dans lequel toutes les pages spécifiques aux vues que vous créez s’affichent, *encapsulées* dans la page de disposition.</span><span class="sxs-lookup"><span data-stu-id="1e47a-288">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="1e47a-289">Par exemple, si vous sélectionnez le lien **Confidentialité**, la vue **Views/Home/Privacy.cshtml** est restituée dans la méthode `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-289">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="1e47a-290">Changer le lien de titre, de pied de page et de menu dans le fichier de disposition</span><span class="sxs-lookup"><span data-stu-id="1e47a-290">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="1e47a-291">Dans les éléments de titre et de pied de page, remplacez `MvcMovie` par `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-291">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="1e47a-292">Modifier l’élément d’ancrage `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` par `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-292">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="1e47a-293">Le balisage suivant illustre les changements en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="1e47a-293">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="1e47a-294">Dans le balisage précédent, l’ [attribut Tag Helper Ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)`asp-area` a été omis, car cette application n’utilise pas de [zones](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="1e47a-294">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="1e47a-295">**Remarque**: le `Movies` contrôleur n’a pas été implémenté.</span><span class="sxs-lookup"><span data-stu-id="1e47a-295">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="1e47a-296">À ce stade, le lien `Movie App` ne fonctionne pas.</span><span class="sxs-lookup"><span data-stu-id="1e47a-296">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="1e47a-297">Enregistrer vos modifications et sélectionnez le lien **Confidentialité**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-297">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="1e47a-298">Notez comment le titre sur l’onglet du navigateur affiche **Stratégie de confidentialité - Movie App** au lieu de **Stratégie de confidentialité - Mvc Movie** :</span><span class="sxs-lookup"><span data-stu-id="1e47a-298">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Onglet Confidentialité](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="1e47a-300">Sélectionnez le lien **Accueil** et notez que le titre et le texte d’ancrage affichent également **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="1e47a-300">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="1e47a-301">Nous avons pu effectuer ce changement une fois dans le modèle de disposition et avoir le nouveau texte de lien et le nouveau titre reflétés sur toutes les pages du site.</span><span class="sxs-lookup"><span data-stu-id="1e47a-301">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="1e47a-302">Examinez le fichier *Views/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1e47a-302">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="1e47a-303">Le fichier *Views/_ViewStart.cshtml* introduit le fichier *Views/Shared/_Layout.cshtml* dans chaque vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-303">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="1e47a-304">La propriété `Layout` peut être utilisée pour définir un mode de disposition différent ou lui affecter la valeur `null`. Aucun fichier de disposition n’est donc utilisé.</span><span class="sxs-lookup"><span data-stu-id="1e47a-304">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="1e47a-305">Modifiez le titre et l’élément `<h2>` de le fichier de vue *Views/HelloWorld/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="1e47a-305">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="1e47a-306">Le titre et l’élément `<h2>` sont légèrement différents afin que vous puissiez voir quel morceau du code modifie l’affichage.</span><span class="sxs-lookup"><span data-stu-id="1e47a-306">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="1e47a-307">Dans le code ci-dessus, `ViewData["Title"] = "Movie List";` définit la propriété `Title` du dictionnaire `ViewData` sur « Movie List ».</span><span class="sxs-lookup"><span data-stu-id="1e47a-307">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="1e47a-308">La propriété `Title` est utilisée dans l’élément HTML `<title>` dans la page de disposition :</span><span class="sxs-lookup"><span data-stu-id="1e47a-308">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="1e47a-309">Enregistrez la modification et accédez à `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-309">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="1e47a-310">Notez que le titre du navigateur, l’en-tête principal et les en-têtes secondaires ont changé.</span><span class="sxs-lookup"><span data-stu-id="1e47a-310">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="1e47a-311">(Si vous ne voyez pas les changements dans le navigateur, vous voyez peut-être le contenu mis en cache.</span><span class="sxs-lookup"><span data-stu-id="1e47a-311">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="1e47a-312">Appuyez sur CTRL + F5 dans votre navigateur pour forcer le chargement de la réponse du serveur.) Le titre du navigateur est créé avec la `ViewData["Title"]` valeur définie dans le modèle d’affichage *index. cshtml* et l’application « -Movie » supplémentaire ajoutée au fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="1e47a-312">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="1e47a-313">Notez également comment le contenu du modèle de vue *Index.cshtml* a été fusionné avec le modèle de vue *Views/Shared/_Layout.cshtml* et qu’une seule réponse HTML a été envoyée au navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-313">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="1e47a-314">Les modèles de disposition permettent d’apporter facilement des modifications qui s’appliquent à toutes les pages de votre application.</span><span class="sxs-lookup"><span data-stu-id="1e47a-314">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="1e47a-315">Pour en savoir plus, consultez [disposition](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="1e47a-315">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Vue Movie List](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="1e47a-317">Nos quelques « données » (dans le cas présent, le message « Hello from our View Template! »</span><span class="sxs-lookup"><span data-stu-id="1e47a-317">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="1e47a-318">) sont toutefois codées en dur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-318">message) is hard-coded, though.</span></span> <span data-ttu-id="1e47a-319">L’application MVC a une vue (« V») et vous avez un contrôleur (« C »), mais pas encore de modèle (« M »).</span><span class="sxs-lookup"><span data-stu-id="1e47a-319">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="1e47a-320">Passage de données du contrôleur vers la vue</span><span class="sxs-lookup"><span data-stu-id="1e47a-320">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="1e47a-321">Les actions du contrôleur sont appelées en réponse à une demande d’URL entrante.</span><span class="sxs-lookup"><span data-stu-id="1e47a-321">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="1e47a-322">Une classe de contrôleur est l’endroit où le code est écrit et qui gère les demandes du navigateur entrantes.</span><span class="sxs-lookup"><span data-stu-id="1e47a-322">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="1e47a-323">Le contrôleur récupère les données d’une source de données et détermine le type de réponse à envoyer au navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-323">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="1e47a-324">Il est possible d’utiliser des modèles de vue à partir d’un contrôleur pour générer et mettre en forme une réponse HTML au navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-324">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="1e47a-325">Les contrôleurs sont chargés de fournir les données nécessaires pour qu’un modèle de vue restitue une réponse.</span><span class="sxs-lookup"><span data-stu-id="1e47a-325">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="1e47a-326">N’oubliez pas que les modèles de vue ne doivent **pas** exécuter de logique métier ou interagir directement avec une base de données.</span><span class="sxs-lookup"><span data-stu-id="1e47a-326">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="1e47a-327">Au lieu de cela, un modèle de vue doit fonctionner uniquement avec les données que le contrôleur lui fournit.</span><span class="sxs-lookup"><span data-stu-id="1e47a-327">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="1e47a-328">Préserver cette « séparation des intérêts » permet de maintenir le code clair, testable et facile à gérer.</span><span class="sxs-lookup"><span data-stu-id="1e47a-328">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="1e47a-329">Actuellement, le `Welcome` méthode présente dans la classe `HelloWorldController` prend un paramètre `name` et un paramètre `ID`, puis sort les valeurs directement dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-329">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="1e47a-330">Au lieu que le contrôleur restitue cette réponse sous forme de chaîne, changez le contrôleur pour qu’il utilise un modèle de vue à la place.</span><span class="sxs-lookup"><span data-stu-id="1e47a-330">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="1e47a-331">Comme le modèle de vue génère une réponse dynamique, les bits de données appropriés doivent être passés du contrôleur à la vue pour générer la réponse.</span><span class="sxs-lookup"><span data-stu-id="1e47a-331">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="1e47a-332">Pour cela, le contrôleur doit placer les données dynamiques (paramètres) dont le modèle de vue a besoin dans un dictionnaire `ViewData` auquel le modèle de vue peut ensuite accéder.</span><span class="sxs-lookup"><span data-stu-id="1e47a-332">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="1e47a-333">Dans *HelloWorldController.cs*, modifiez la méthode `Welcome` pour ajouter une valeur `Message` et `NumTimes` au dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-333">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="1e47a-334">Le dictionnaire `ViewData` est un objet dynamique, ce qui signifie que n’importe quel type peut être utilisé, l’objet `ViewData` ne possède aucune propriété définie tant que vous ne placez pas d’élément dedans.</span><span class="sxs-lookup"><span data-stu-id="1e47a-334">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="1e47a-335">Le [système de liaison de données MVC](xref:mvc/models/model-binding) mappe automatiquement les paramètres nommés (`name` et `numTimes`) provenant de la chaîne de requête dans la barre d’adresse aux paramètres de votre méthode.</span><span class="sxs-lookup"><span data-stu-id="1e47a-335">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="1e47a-336">Le fichier *HelloWorldController.cs* complet ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="1e47a-336">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="1e47a-337">L’objet dictionnaire `ViewData` contient des données qui seront passées à la vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-337">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="1e47a-338">Créez un modèle de vue Welcome nommé *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="1e47a-338">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="1e47a-339">Vous allez créer une boucle dans le modèle de vue *Welcome.cshtml* qui affiche « Hello » `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-339">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="1e47a-340">Remplacez le contenu de *Views/HelloWorld/Welcome.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="1e47a-340">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="1e47a-341">Enregistrez vos modifications et accédez à l’URL suivante :</span><span class="sxs-lookup"><span data-stu-id="1e47a-341">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="1e47a-342">Les données sont extraites de l’URL et passées au contrôleur à l’aide du [classeur de modèles MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="1e47a-342">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="1e47a-343">Le contrôleur empaquette les données dans un dictionnaire `ViewData` et passe cet objet à la vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-343">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="1e47a-344">La vue restitue ensuite les données au format HTML dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="1e47a-344">The view then renders the data as HTML to the browser.</span></span>

![Vue Confidentialité affichant une étiquette Welcome et l’expression « Hello Rick » affichée quatre fois](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="1e47a-346">Dans l’exemple ci-dessus, le dictionnaire `ViewData` a été utilisé pour passer des données du contrôleur à une vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-346">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="1e47a-347">Plus loin dans ce didacticiel, un modèle de vue est utilisé pour passer les données d’un contrôleur à une vue.</span><span class="sxs-lookup"><span data-stu-id="1e47a-347">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="1e47a-348">L’approche basée sur le modèle de vue pour passer des données est généralement préférée à l’approche basée sur le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="1e47a-348">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="1e47a-349">Pour plus d’informations, consultez [Quand utiliser ViewBag, ViewData ou TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="1e47a-349">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="1e47a-350">Dans le didacticiel suivant, une base de données de films est créée.</span><span class="sxs-lookup"><span data-stu-id="1e47a-350">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="1e47a-351">[Précédent](adding-controller.md) 
>  [Suivant](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="1e47a-351">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
