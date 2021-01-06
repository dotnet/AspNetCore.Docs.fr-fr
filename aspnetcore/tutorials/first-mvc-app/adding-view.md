---
title: 'Partie 3 : ajouter une vue à une application ASP.NET Core MVC'
author: rick-anderson
description: Partie 3 de la série de didacticiels sur ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2019
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
ms.openlocfilehash: ce2754d22b61578bdbe5e78a69e08bdc1e83190d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94688348"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="02642-103">Partie 3 : ajouter une vue à une application ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="02642-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="02642-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="02642-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02642-105">Dans cette section, vous allez modifier la `HelloWorldController` classe pour utiliser [Razor](xref:mvc/views/razor) des fichiers de vue afin d’encapsuler correctement le processus de génération de réponses html sur un client.</span><span class="sxs-lookup"><span data-stu-id="02642-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="02642-106">Vous créez un fichier de modèle de vue à l’aide de Razor .</span><span class="sxs-lookup"><span data-stu-id="02642-106">You create a view template file using Razor.</span></span> <span data-ttu-id="02642-107">Razorles modèles de vue basés sur utilisent une extension de fichier *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="02642-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="02642-108">Ils offrent un moyen élégant pour créer une sortie HTML avec C#.</span><span class="sxs-lookup"><span data-stu-id="02642-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="02642-109">Actuellement, la méthode `Index` retourne une chaîne avec un message qui est codé en dur dans la classe du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="02642-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="02642-110">Dans la classe `HelloWorldController`, remplacez la méthode `Index` par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="02642-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="02642-111">Le code précédent appelle la méthode <xref:Microsoft.AspNetCore.Mvc.Controller.View*> du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="02642-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="02642-112">Il utilise un modèle de vue pour générer une réponse HTML.</span><span class="sxs-lookup"><span data-stu-id="02642-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="02642-113">Les méthodes du contrôleur (également appelées *méthodes d’action*), comme la méthode `Index` ci-dessus, retournent généralement un <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou une classe dérivée de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), et non un type comme `string`.</span><span class="sxs-lookup"><span data-stu-id="02642-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="02642-114">Ajouter une vue</span><span class="sxs-lookup"><span data-stu-id="02642-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02642-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02642-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02642-116">Cliquez avec le bouton droit sur le dossier *Vues*, cliquez sur **Ajouter > Nouveau dossier**, puis nommez le dossier *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="02642-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="02642-117">Cliquez avec le bouton droit sur le dossier *Vues/HelloWorld*, puis cliquez sur **Ajouter > Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="02642-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="02642-118">Dans la boîte de dialogue **Ajouter un nouvel élément - MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="02642-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="02642-119">Dans la zone de recherche située en haut à droite, entrez *vue*</span><span class="sxs-lookup"><span data-stu-id="02642-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="02642-120">Sélectionner une **Razor vue**</span><span class="sxs-lookup"><span data-stu-id="02642-120">Select **Razor View**</span></span>

  * <span data-ttu-id="02642-121">Conservez la valeur de la zone **Nom**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="02642-122">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="02642-122">Select **Add**</span></span>

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="02642-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02642-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02642-125">Ajoutez une vue `Index` pour `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="02642-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="02642-126">Ajoutez un nouveau dossier nommé *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="02642-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="02642-127">Ajoutez un nouveau fichier à la *vues/HelloWorld* nom du dossier *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02642-128">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="02642-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02642-129">Cliquez avec le bouton droit sur le dossier *Vues*, cliquez sur **Ajouter > Nouveau dossier**, puis nommez le dossier *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="02642-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="02642-130">Cliquez avec le bouton droit sur le dossier *Vues/HelloWorld*, puis cliquez sur **Ajouter > Nouveau fichier**.</span><span class="sxs-lookup"><span data-stu-id="02642-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="02642-131">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="02642-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="02642-132">Sélectionnez **ASP .net Core** dans le volet gauche.</span><span class="sxs-lookup"><span data-stu-id="02642-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="02642-133">Sélectionnez **Razor affichage** dans le volet central.</span><span class="sxs-lookup"><span data-stu-id="02642-133">Select **Razor View** in the center pane.</span></span>
  * <span data-ttu-id="02642-134">Dans la zone **nom** , tapez *index* .</span><span class="sxs-lookup"><span data-stu-id="02642-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="02642-135">Sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="02642-135">Select **New**.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="02642-137">Remplacez le contenu du fichier de vue *views/HelloWorld/index. cshtml* Razor par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="02642-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="02642-138">Accédez à `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="02642-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="02642-139">La méthode `Index` dans `HelloWorldController` n’a pas accompli beaucoup d’actions. Elle a exécuté l’instruction `return View();`, laquelle spécifiait que la méthode doit utiliser un fichier de modèle de vue pour restituer une réponse au navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="02642-140">Comme aucun nom de fichier de modèle de vue n’a été spécifié, MVC utilise le fichier d’affichage par défaut.</span><span class="sxs-lookup"><span data-stu-id="02642-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="02642-141">Le fichier d’affichage par défaut porte le même nom que la méthode ( `Index` ), le modèle de vue dans */views/HelloWorld/index.cshtml* est donc utilisé.</span><span class="sxs-lookup"><span data-stu-id="02642-141">The default view file has the same name as the method (`Index`), so the view template in */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="02642-142">L’image ci-dessous montre la chaîne « Hello from our View Template! »</span><span class="sxs-lookup"><span data-stu-id="02642-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="02642-143">codée en dur dans la vue.</span><span class="sxs-lookup"><span data-stu-id="02642-143">hard-coded in the view.</span></span>

![Fenêtre du navigateur](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="02642-145">Changer les vues et les pages de disposition</span><span class="sxs-lookup"><span data-stu-id="02642-145">Change views and layout pages</span></span>

<span data-ttu-id="02642-146">Sélectionnez les liens du menu (**MvcMovie**, **Accueil** et **Confidentialité**).</span><span class="sxs-lookup"><span data-stu-id="02642-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="02642-147">Chaque page affiche la même disposition de menu.</span><span class="sxs-lookup"><span data-stu-id="02642-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="02642-148">La disposition du menu est implémentée dans le fichier *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="02642-149">Ouvrez le fichier *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="02642-150">Les modèles de [disposition](xref:mvc/views/layout) vous permettent de spécifier la disposition du conteneur HTML de votre site dans un emplacement unique, puis de l’appliquer sur plusieurs pages de votre site.</span><span class="sxs-lookup"><span data-stu-id="02642-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="02642-151">Recherchez la ligne `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="02642-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="02642-152">`RenderBody` est un espace réservé dans lequel toutes les pages spécifiques aux vues que vous créez s’affichent, *encapsulées* dans la page de disposition.</span><span class="sxs-lookup"><span data-stu-id="02642-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="02642-153">Par exemple, si vous sélectionnez le lien **Confidentialité**, la vue **Views/Home/Privacy.cshtml** est restituée dans la méthode `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="02642-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="02642-154">Changer le lien de titre, de pied de page et de menu dans le fichier de disposition</span><span class="sxs-lookup"><span data-stu-id="02642-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="02642-155">Remplacez le contenu du fichier *Views/Shared/_Layout. cshtml* par le balisage suivant.</span><span class="sxs-lookup"><span data-stu-id="02642-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="02642-156">Les modifications apparaissent en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="02642-156">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="02642-157">Le balisage précédent apporte les modifications suivantes :</span><span class="sxs-lookup"><span data-stu-id="02642-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="02642-158">Remplacement de 3 occurrences de `MvcMovie` par `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="02642-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="02642-159">Remplacement de l’élément d’ancrage `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` par `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="02642-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="02642-160">Dans le balisage précédent, l’ [attribut Tag Helper d’ancrage `asp-area=""`](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) et la valeur d’attribut ont été omis, car cette application n’utilise pas de [zones](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="02642-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="02642-161">**Remarque**: le `Movies` contrôleur n’a pas été implémenté.</span><span class="sxs-lookup"><span data-stu-id="02642-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="02642-162">À ce stade, le lien `Movie App` ne fonctionne pas.</span><span class="sxs-lookup"><span data-stu-id="02642-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="02642-163">Enregistrer vos modifications et sélectionnez le lien **Confidentialité**.</span><span class="sxs-lookup"><span data-stu-id="02642-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="02642-164">Notez comment le titre sur l’onglet du navigateur affiche **Stratégie de confidentialité - Movie App** au lieu de **Stratégie de confidentialité - Mvc Movie** :</span><span class="sxs-lookup"><span data-stu-id="02642-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Onglet Confidentialité](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="02642-166">Sélectionnez le lien **Accueil** et notez que le titre et le texte d’ancrage affichent également **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="02642-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="02642-167">Nous avons pu effectuer ce changement une fois dans le modèle de disposition et avoir le nouveau texte de lien et le nouveau titre reflétés sur toutes les pages du site.</span><span class="sxs-lookup"><span data-stu-id="02642-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="02642-168">Examinez le fichier *Views/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="02642-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="02642-169">Le fichier *Views/_ViewStart.cshtml* introduit le fichier *Views/Shared/_Layout.cshtml* dans chaque vue.</span><span class="sxs-lookup"><span data-stu-id="02642-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="02642-170">La propriété `Layout` peut être utilisée pour définir un mode de disposition différent ou lui affecter la valeur `null`. Aucun fichier de disposition n’est donc utilisé.</span><span class="sxs-lookup"><span data-stu-id="02642-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="02642-171">Modifiez le titre et l’élément `<h2>` de le fichier de vue *Views/HelloWorld/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="02642-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="02642-172">Le titre et l’élément `<h2>` sont légèrement différents afin que vous puissiez voir quel morceau du code modifie l’affichage.</span><span class="sxs-lookup"><span data-stu-id="02642-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="02642-173">Dans le code ci-dessus, `ViewData["Title"] = "Movie List";` définit la propriété `Title` du dictionnaire `ViewData` sur « Movie List ».</span><span class="sxs-lookup"><span data-stu-id="02642-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="02642-174">La propriété `Title` est utilisée dans l’élément HTML `<title>` dans la page de disposition :</span><span class="sxs-lookup"><span data-stu-id="02642-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="02642-175">Enregistrez la modification et accédez à `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="02642-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="02642-176">Notez que le titre du navigateur, l’en-tête principal et les en-têtes secondaires ont changé.</span><span class="sxs-lookup"><span data-stu-id="02642-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="02642-177">(Si vous ne voyez pas les changements dans le navigateur, vous voyez peut-être le contenu mis en cache.</span><span class="sxs-lookup"><span data-stu-id="02642-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="02642-178">Appuyez sur CTRL + F5 dans votre navigateur pour forcer le chargement de la réponse du serveur.) Le titre du navigateur est créé avec la `ViewData["Title"]` valeur définie dans le modèle d’affichage *index. cshtml* et l’application « -Movie » supplémentaire ajoutée au fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="02642-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="02642-179">Le contenu du modèle de vue *Index.cshtml* est fusionné avec le modèle de vue *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="02642-180">Une réponse HTML unique est envoyée au navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="02642-181">Les modèles de disposition permettent d’apporter facilement des modifications qui s’appliquent à toutes les pages d’une application.</span><span class="sxs-lookup"><span data-stu-id="02642-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="02642-182">Pour en savoir plus, consultez [Disposition](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="02642-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Vue Movie List](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="02642-184">Nos quelques « données » (dans le cas présent, le message « Hello from our View Template! »</span><span class="sxs-lookup"><span data-stu-id="02642-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="02642-185">) sont toutefois codées en dur.</span><span class="sxs-lookup"><span data-stu-id="02642-185">message) is hard-coded, though.</span></span> <span data-ttu-id="02642-186">L’application MVC a une vue (« V») et vous avez un contrôleur (« C »), mais pas encore de modèle (« M »).</span><span class="sxs-lookup"><span data-stu-id="02642-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="02642-187">Passage de données du contrôleur vers la vue</span><span class="sxs-lookup"><span data-stu-id="02642-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="02642-188">Les actions du contrôleur sont appelées en réponse à une demande d’URL entrante.</span><span class="sxs-lookup"><span data-stu-id="02642-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="02642-189">Une classe de contrôleur est l’endroit où le code est écrit et qui gère les demandes du navigateur entrantes.</span><span class="sxs-lookup"><span data-stu-id="02642-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="02642-190">Le contrôleur récupère les données d’une source de données et détermine le type de réponse à envoyer au navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="02642-191">Il est possible d’utiliser des modèles de vue à partir d’un contrôleur pour générer et mettre en forme une réponse HTML au navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="02642-192">Les contrôleurs sont chargés de fournir les données nécessaires pour qu’un modèle de vue restitue une réponse.</span><span class="sxs-lookup"><span data-stu-id="02642-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="02642-193">N’oubliez pas que les modèles de vue ne doivent **pas** exécuter de logique métier ou interagir directement avec une base de données.</span><span class="sxs-lookup"><span data-stu-id="02642-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="02642-194">Au lieu de cela, un modèle de vue doit fonctionner uniquement avec les données que le contrôleur lui fournit.</span><span class="sxs-lookup"><span data-stu-id="02642-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="02642-195">Préserver cette « séparation des intérêts » permet de maintenir le code clair, testable et facile à gérer.</span><span class="sxs-lookup"><span data-stu-id="02642-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="02642-196">Actuellement, le `Welcome` méthode présente dans la classe `HelloWorldController` prend un paramètre `name` et un paramètre `ID`, puis sort les valeurs directement dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="02642-197">Au lieu que le contrôleur restitue cette réponse sous forme de chaîne, changez le contrôleur pour qu’il utilise un modèle de vue à la place.</span><span class="sxs-lookup"><span data-stu-id="02642-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="02642-198">Comme le modèle de vue génère une réponse dynamique, les bits de données appropriés doivent être passés du contrôleur à la vue pour générer la réponse.</span><span class="sxs-lookup"><span data-stu-id="02642-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="02642-199">Pour cela, le contrôleur doit placer les données dynamiques (paramètres) dont le modèle de vue a besoin dans un dictionnaire `ViewData` auquel le modèle de vue peut ensuite accéder.</span><span class="sxs-lookup"><span data-stu-id="02642-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="02642-200">Dans *HelloWorldController.cs*, modifiez la méthode `Welcome` pour ajouter une valeur `Message` et `NumTimes` au dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="02642-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="02642-201">Le dictionnaire `ViewData` est un objet dynamique, ce qui signifie que n’importe quel type peut être utilisé, l’objet `ViewData` ne possède aucune propriété définie tant que vous ne placez pas d’élément dedans.</span><span class="sxs-lookup"><span data-stu-id="02642-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="02642-202">Le [système de liaison de données MVC](xref:mvc/models/model-binding) mappe automatiquement les paramètres nommés (`name` et `numTimes`) provenant de la chaîne de requête dans la barre d’adresse aux paramètres de votre méthode.</span><span class="sxs-lookup"><span data-stu-id="02642-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="02642-203">Le fichier *HelloWorldController.cs* complet ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="02642-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="02642-204">L’objet dictionnaire `ViewData` contient des données qui seront passées à la vue.</span><span class="sxs-lookup"><span data-stu-id="02642-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="02642-205">Créez un modèle de vue Welcome nommé *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="02642-206">Vous allez créer une boucle dans le modèle de vue *Welcome.cshtml* qui affiche « Hello » `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="02642-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="02642-207">Remplacez le contenu de *Views/HelloWorld/Welcome.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="02642-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="02642-208">Enregistrez vos modifications et accédez à l’URL suivante :</span><span class="sxs-lookup"><span data-stu-id="02642-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="02642-209">Les données sont extraites de l’URL et passées au contrôleur à l’aide du [classeur de modèles MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="02642-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="02642-210">Le contrôleur empaquette les données dans un dictionnaire `ViewData` et passe cet objet à la vue.</span><span class="sxs-lookup"><span data-stu-id="02642-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="02642-211">La vue restitue ensuite les données au format HTML dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-211">The view then renders the data as HTML to the browser.</span></span>

![Vue Confidentialité affichant une étiquette Welcome et l’expression « Hello Rick » affichée quatre fois](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="02642-213">Dans l’exemple ci-dessus, le dictionnaire `ViewData` a été utilisé pour passer des données du contrôleur à une vue.</span><span class="sxs-lookup"><span data-stu-id="02642-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="02642-214">Plus loin dans ce didacticiel, un modèle de vue est utilisé pour passer les données d’un contrôleur à une vue.</span><span class="sxs-lookup"><span data-stu-id="02642-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="02642-215">L’approche basée sur le modèle de vue pour passer des données est généralement préférée à l’approche basée sur le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="02642-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="02642-216">Pour plus d’informations, consultez [Quand utiliser ViewBag, ViewData ou TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="02642-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="02642-217">Dans le didacticiel suivant, une base de données de films est créée.</span><span class="sxs-lookup"><span data-stu-id="02642-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="02642-218">[Précédent](adding-controller.md) 
>  [Suivant](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="02642-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="02642-219">Dans cette section, vous allez modifier la `HelloWorldController` classe pour utiliser [Razor](xref:mvc/views/razor) des fichiers de vue afin d’encapsuler correctement le processus de génération de réponses html sur un client.</span><span class="sxs-lookup"><span data-stu-id="02642-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="02642-220">Vous créez un fichier de modèle de vue à l’aide de Razor .</span><span class="sxs-lookup"><span data-stu-id="02642-220">You create a view template file using Razor.</span></span> <span data-ttu-id="02642-221">Razorles modèles de vue basés sur utilisent une extension de fichier *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="02642-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="02642-222">Ils offrent un moyen élégant pour créer une sortie HTML avec C#.</span><span class="sxs-lookup"><span data-stu-id="02642-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="02642-223">Actuellement, la méthode `Index` retourne une chaîne avec un message qui est codé en dur dans la classe du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="02642-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="02642-224">Dans la classe `HelloWorldController`, remplacez la méthode `Index` par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="02642-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="02642-225">Le code précédent appelle la méthode <xref:Microsoft.AspNetCore.Mvc.Controller.View*> du contrôleur.</span><span class="sxs-lookup"><span data-stu-id="02642-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="02642-226">Il utilise un modèle de vue pour générer une réponse HTML.</span><span class="sxs-lookup"><span data-stu-id="02642-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="02642-227">Les méthodes du contrôleur (également appelées *méthodes d’action*), comme la méthode `Index` ci-dessus, retournent généralement un <xref:Microsoft.AspNetCore.Mvc.IActionResult> (ou une classe dérivée de <xref:Microsoft.AspNetCore.Mvc.ActionResult>), et non un type comme `string`.</span><span class="sxs-lookup"><span data-stu-id="02642-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="02642-228">Ajouter une vue</span><span class="sxs-lookup"><span data-stu-id="02642-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="02642-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="02642-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="02642-230">Cliquez avec le bouton droit sur le dossier *Vues*, cliquez sur **Ajouter > Nouveau dossier**, puis nommez le dossier *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="02642-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="02642-231">Cliquez avec le bouton droit sur le dossier *Vues/HelloWorld*, puis cliquez sur **Ajouter > Nouvel élément**.</span><span class="sxs-lookup"><span data-stu-id="02642-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="02642-232">Dans la boîte de dialogue **Ajouter un nouvel élément - MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="02642-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="02642-233">Dans la zone de recherche située en haut à droite, entrez *vue*</span><span class="sxs-lookup"><span data-stu-id="02642-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="02642-234">Sélectionner une **Razor vue**</span><span class="sxs-lookup"><span data-stu-id="02642-234">Select **Razor View**</span></span>

  * <span data-ttu-id="02642-235">Conservez la valeur de la zone **Nom**, *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="02642-236">Sélectionnez **Ajouter**</span><span class="sxs-lookup"><span data-stu-id="02642-236">Select **Add**</span></span>

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="02642-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="02642-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="02642-239">Ajoutez une vue `Index` pour `HelloWorldController`.</span><span class="sxs-lookup"><span data-stu-id="02642-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="02642-240">Ajoutez un nouveau dossier nommé *Views/HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="02642-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="02642-241">Ajoutez un nouveau fichier à la *vues/HelloWorld* nom du dossier *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="02642-242">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="02642-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="02642-243">Cliquez avec le bouton droit sur le dossier *Vues*, cliquez sur **Ajouter > Nouveau dossier**, puis nommez le dossier *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="02642-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="02642-244">Cliquez avec le bouton droit sur le dossier *Vues/HelloWorld*, puis cliquez sur **Ajouter > Nouveau fichier**.</span><span class="sxs-lookup"><span data-stu-id="02642-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="02642-245">Dans la boîte de dialogue **Nouveau fichier** :</span><span class="sxs-lookup"><span data-stu-id="02642-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="02642-246">Sélectionnez **Web** dans le volet gauche.</span><span class="sxs-lookup"><span data-stu-id="02642-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="02642-247">Sélectionnez **Fichier HTML vide** dans le volet central.</span><span class="sxs-lookup"><span data-stu-id="02642-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="02642-248">Tapez *Index.cshtml* dans la zone **Nom**.</span><span class="sxs-lookup"><span data-stu-id="02642-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="02642-249">Sélectionnez **Nouveau**.</span><span class="sxs-lookup"><span data-stu-id="02642-249">Select **New**.</span></span>

![Boîte de dialogue Ajouter un nouvel élément](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="02642-251">Remplacez le contenu du fichier de vue *views/HelloWorld/index. cshtml* Razor par ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="02642-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="02642-252">Accédez à `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="02642-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="02642-253">La méthode `Index` dans `HelloWorldController` n’a pas accompli beaucoup d’actions. Elle a exécuté l’instruction `return View();`, laquelle spécifiait que la méthode doit utiliser un fichier de modèle de vue pour restituer une réponse au navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="02642-254">Comme aucun nom de fichier de modèle de vue n’a été spécifié, MVC utilise le fichier d’affichage par défaut.</span><span class="sxs-lookup"><span data-stu-id="02642-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="02642-255">Le fichier d’affichage par défaut a le même nom que la méthode (`Index`), donc */Views/HelloWorld/Index.cshtml* est utilisé.</span><span class="sxs-lookup"><span data-stu-id="02642-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="02642-256">L’image ci-dessous montre la chaîne « Hello from our View Template! »</span><span class="sxs-lookup"><span data-stu-id="02642-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="02642-257">codée en dur dans la vue.</span><span class="sxs-lookup"><span data-stu-id="02642-257">hard-coded in the view.</span></span>

![Fenêtre du navigateur](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="02642-259">Changer les vues et les pages de disposition</span><span class="sxs-lookup"><span data-stu-id="02642-259">Change views and layout pages</span></span>

<span data-ttu-id="02642-260">Sélectionnez les liens du menu (**MvcMovie**, **Accueil** et **Confidentialité**).</span><span class="sxs-lookup"><span data-stu-id="02642-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="02642-261">Chaque page affiche la même disposition de menu.</span><span class="sxs-lookup"><span data-stu-id="02642-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="02642-262">La disposition du menu est implémentée dans le fichier *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="02642-263">Ouvrez le fichier *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="02642-264">Les modèles de [disposition](xref:mvc/views/layout) vous permettent de spécifier la disposition du conteneur HTML de votre site dans un emplacement unique, puis de l’appliquer sur plusieurs pages de votre site.</span><span class="sxs-lookup"><span data-stu-id="02642-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="02642-265">Recherchez la ligne `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="02642-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="02642-266">`RenderBody` est un espace réservé dans lequel toutes les pages spécifiques aux vues que vous créez s’affichent, *encapsulées* dans la page de disposition.</span><span class="sxs-lookup"><span data-stu-id="02642-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="02642-267">Par exemple, si vous sélectionnez le lien **Confidentialité**, la vue **Views/Home/Privacy.cshtml** est restituée dans la méthode `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="02642-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="02642-268">Changer le lien de titre, de pied de page et de menu dans le fichier de disposition</span><span class="sxs-lookup"><span data-stu-id="02642-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="02642-269">Dans les éléments de titre et de pied de page, remplacez `MvcMovie` par `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="02642-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="02642-270">Modifier l’élément d’ancrage `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` par `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="02642-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="02642-271">Le balisage suivant illustre les changements en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="02642-271">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="02642-272">Dans le balisage précédent, l’ [attribut Tag Helper Ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)`asp-area` a été omis, car cette application n’utilise pas de [zones](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="02642-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="02642-273">**Remarque**: le `Movies` contrôleur n’a pas été implémenté.</span><span class="sxs-lookup"><span data-stu-id="02642-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="02642-274">À ce stade, le lien `Movie App` ne fonctionne pas.</span><span class="sxs-lookup"><span data-stu-id="02642-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="02642-275">Enregistrer vos modifications et sélectionnez le lien **Confidentialité**.</span><span class="sxs-lookup"><span data-stu-id="02642-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="02642-276">Notez comment le titre sur l’onglet du navigateur affiche **Stratégie de confidentialité - Movie App** au lieu de **Stratégie de confidentialité - Mvc Movie** :</span><span class="sxs-lookup"><span data-stu-id="02642-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Onglet Confidentialité](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="02642-278">Sélectionnez le lien **Accueil** et notez que le titre et le texte d’ancrage affichent également **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="02642-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="02642-279">Nous avons pu effectuer ce changement une fois dans le modèle de disposition et avoir le nouveau texte de lien et le nouveau titre reflétés sur toutes les pages du site.</span><span class="sxs-lookup"><span data-stu-id="02642-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="02642-280">Examinez le fichier *Views/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="02642-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="02642-281">Le fichier *Views/_ViewStart.cshtml* introduit le fichier *Views/Shared/_Layout.cshtml* dans chaque vue.</span><span class="sxs-lookup"><span data-stu-id="02642-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="02642-282">La propriété `Layout` peut être utilisée pour définir un mode de disposition différent ou lui affecter la valeur `null`. Aucun fichier de disposition n’est donc utilisé.</span><span class="sxs-lookup"><span data-stu-id="02642-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="02642-283">Modifiez le titre et l’élément `<h2>` de le fichier de vue *Views/HelloWorld/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="02642-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="02642-284">Le titre et l’élément `<h2>` sont légèrement différents afin que vous puissiez voir quel morceau du code modifie l’affichage.</span><span class="sxs-lookup"><span data-stu-id="02642-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="02642-285">Dans le code ci-dessus, `ViewData["Title"] = "Movie List";` définit la propriété `Title` du dictionnaire `ViewData` sur « Movie List ».</span><span class="sxs-lookup"><span data-stu-id="02642-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="02642-286">La propriété `Title` est utilisée dans l’élément HTML `<title>` dans la page de disposition :</span><span class="sxs-lookup"><span data-stu-id="02642-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="02642-287">Enregistrez la modification et accédez à `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="02642-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="02642-288">Notez que le titre du navigateur, l’en-tête principal et les en-têtes secondaires ont changé.</span><span class="sxs-lookup"><span data-stu-id="02642-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="02642-289">(Si vous ne voyez pas les changements dans le navigateur, vous voyez peut-être le contenu mis en cache.</span><span class="sxs-lookup"><span data-stu-id="02642-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="02642-290">Appuyez sur CTRL + F5 dans votre navigateur pour forcer le chargement de la réponse du serveur.) Le titre du navigateur est créé avec la `ViewData["Title"]` valeur définie dans le modèle d’affichage *index. cshtml* et l’application « -Movie » supplémentaire ajoutée au fichier de disposition.</span><span class="sxs-lookup"><span data-stu-id="02642-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="02642-291">Notez également comment le contenu du modèle de vue *Index.cshtml* a été fusionné avec le modèle de vue *Views/Shared/_Layout.cshtml* et qu’une seule réponse HTML a été envoyée au navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="02642-292">Les modèles de disposition permettent d’apporter facilement des modifications qui s’appliquent à toutes les pages de votre application.</span><span class="sxs-lookup"><span data-stu-id="02642-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="02642-293">Pour en savoir plus, consultez [disposition](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="02642-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Vue Movie List](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="02642-295">Nos quelques « données » (dans le cas présent, le message « Hello from our View Template! »</span><span class="sxs-lookup"><span data-stu-id="02642-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="02642-296">) sont toutefois codées en dur.</span><span class="sxs-lookup"><span data-stu-id="02642-296">message) is hard-coded, though.</span></span> <span data-ttu-id="02642-297">L’application MVC a une vue (« V») et vous avez un contrôleur (« C »), mais pas encore de modèle (« M »).</span><span class="sxs-lookup"><span data-stu-id="02642-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="02642-298">Passage de données du contrôleur vers la vue</span><span class="sxs-lookup"><span data-stu-id="02642-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="02642-299">Les actions du contrôleur sont appelées en réponse à une demande d’URL entrante.</span><span class="sxs-lookup"><span data-stu-id="02642-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="02642-300">Une classe de contrôleur est l’endroit où le code est écrit et qui gère les demandes du navigateur entrantes.</span><span class="sxs-lookup"><span data-stu-id="02642-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="02642-301">Le contrôleur récupère les données d’une source de données et détermine le type de réponse à envoyer au navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="02642-302">Il est possible d’utiliser des modèles de vue à partir d’un contrôleur pour générer et mettre en forme une réponse HTML au navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="02642-303">Les contrôleurs sont chargés de fournir les données nécessaires pour qu’un modèle de vue restitue une réponse.</span><span class="sxs-lookup"><span data-stu-id="02642-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="02642-304">N’oubliez pas que les modèles de vue ne doivent **pas** exécuter de logique métier ou interagir directement avec une base de données.</span><span class="sxs-lookup"><span data-stu-id="02642-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="02642-305">Au lieu de cela, un modèle de vue doit fonctionner uniquement avec les données que le contrôleur lui fournit.</span><span class="sxs-lookup"><span data-stu-id="02642-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="02642-306">Préserver cette « séparation des intérêts » permet de maintenir le code clair, testable et facile à gérer.</span><span class="sxs-lookup"><span data-stu-id="02642-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="02642-307">Actuellement, le `Welcome` méthode présente dans la classe `HelloWorldController` prend un paramètre `name` et un paramètre `ID`, puis sort les valeurs directement dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="02642-308">Au lieu que le contrôleur restitue cette réponse sous forme de chaîne, changez le contrôleur pour qu’il utilise un modèle de vue à la place.</span><span class="sxs-lookup"><span data-stu-id="02642-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="02642-309">Comme le modèle de vue génère une réponse dynamique, les bits de données appropriés doivent être passés du contrôleur à la vue pour générer la réponse.</span><span class="sxs-lookup"><span data-stu-id="02642-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="02642-310">Pour cela, le contrôleur doit placer les données dynamiques (paramètres) dont le modèle de vue a besoin dans un dictionnaire `ViewData` auquel le modèle de vue peut ensuite accéder.</span><span class="sxs-lookup"><span data-stu-id="02642-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="02642-311">Dans *HelloWorldController.cs*, modifiez la méthode `Welcome` pour ajouter une valeur `Message` et `NumTimes` au dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="02642-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="02642-312">Le dictionnaire `ViewData` est un objet dynamique, ce qui signifie que n’importe quel type peut être utilisé, l’objet `ViewData` ne possède aucune propriété définie tant que vous ne placez pas d’élément dedans.</span><span class="sxs-lookup"><span data-stu-id="02642-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="02642-313">Le [système de liaison de données MVC](xref:mvc/models/model-binding) mappe automatiquement les paramètres nommés (`name` et `numTimes`) provenant de la chaîne de requête dans la barre d’adresse aux paramètres de votre méthode.</span><span class="sxs-lookup"><span data-stu-id="02642-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="02642-314">Le fichier *HelloWorldController.cs* complet ressemble à ceci :</span><span class="sxs-lookup"><span data-stu-id="02642-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="02642-315">L’objet dictionnaire `ViewData` contient des données qui seront passées à la vue.</span><span class="sxs-lookup"><span data-stu-id="02642-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="02642-316">Créez un modèle de vue Welcome nommé *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="02642-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="02642-317">Vous allez créer une boucle dans le modèle de vue *Welcome.cshtml* qui affiche « Hello » `NumTimes`.</span><span class="sxs-lookup"><span data-stu-id="02642-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="02642-318">Remplacez le contenu de *Views/HelloWorld/Welcome.cshtml* avec le code suivant :</span><span class="sxs-lookup"><span data-stu-id="02642-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="02642-319">Enregistrez vos modifications et accédez à l’URL suivante :</span><span class="sxs-lookup"><span data-stu-id="02642-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="02642-320">Les données sont extraites de l’URL et passées au contrôleur à l’aide du [classeur de modèles MVC](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="02642-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="02642-321">Le contrôleur empaquette les données dans un dictionnaire `ViewData` et passe cet objet à la vue.</span><span class="sxs-lookup"><span data-stu-id="02642-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="02642-322">La vue restitue ensuite les données au format HTML dans le navigateur.</span><span class="sxs-lookup"><span data-stu-id="02642-322">The view then renders the data as HTML to the browser.</span></span>

![Vue Confidentialité affichant une étiquette Welcome et l’expression « Hello Rick » affichée quatre fois](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="02642-324">Dans l’exemple ci-dessus, le dictionnaire `ViewData` a été utilisé pour passer des données du contrôleur à une vue.</span><span class="sxs-lookup"><span data-stu-id="02642-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="02642-325">Plus loin dans ce didacticiel, un modèle de vue est utilisé pour passer les données d’un contrôleur à une vue.</span><span class="sxs-lookup"><span data-stu-id="02642-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="02642-326">L’approche basée sur le modèle de vue pour passer des données est généralement préférée à l’approche basée sur le dictionnaire `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="02642-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="02642-327">Pour plus d’informations, consultez [Quand utiliser ViewBag, ViewData ou TempData ](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="02642-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="02642-328">Dans le didacticiel suivant, une base de données de films est créée.</span><span class="sxs-lookup"><span data-stu-id="02642-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="02642-329">[Précédent](adding-controller.md) 
>  [Suivant](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="02642-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
