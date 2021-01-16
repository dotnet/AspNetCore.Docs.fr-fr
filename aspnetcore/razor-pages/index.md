---
title: Présentation Razor des pages dans ASP.net Core
author: Rick-Anderson
description: Explique comment Razor les pages de ASP.net Core rendent le codage des scénarios orientés page plus facile et plus productif que l’utilisation de MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: f8cdbbffae9b291923a6d425fef5526b0ec88f61
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253187"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="eea4e-103">Présentation Razor des pages dans ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="eea4e-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="eea4e-104">De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="eea4e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="eea4e-105">Razor Les pages peuvent rendre le codage des scénarios orientés page plus facile et plus productif que l’utilisation de contrôleurs et de vues.</span><span class="sxs-lookup"><span data-stu-id="eea4e-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="eea4e-106">Si vous cherchez un didacticiel qui utilise l’approche Model-View-Controller, consultez [Bien démarrer avec ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="eea4e-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="eea4e-107">Ce document fournit une introduction aux Razor pages.</span><span class="sxs-lookup"><span data-stu-id="eea4e-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="eea4e-108">Il ne s’agit pas d’un didacticiel pas à pas.</span><span class="sxs-lookup"><span data-stu-id="eea4e-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="eea4e-109">Si vous trouvez certaines des sections trop avancées, consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="eea4e-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="eea4e-110">Pour une vue d’ensemble d’ASP.NET Core, consultez [Introduction à ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="eea4e-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eea4e-111">Prérequis</span><span class="sxs-lookup"><span data-stu-id="eea4e-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="eea4e-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eea4e-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eea4e-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eea4e-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eea4e-114">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="eea4e-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="eea4e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eea4e-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eea4e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eea4e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eea4e-117">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="eea4e-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="eea4e-118">Créer un Razor projet pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eea4e-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eea4e-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eea4e-120">Pour obtenir des instructions détaillées sur la création d’un projet de pages, consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="eea4e-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eea4e-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eea4e-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eea4e-122">Exécutez `dotnet new webapp` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="eea4e-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eea4e-123">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="eea4e-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eea4e-124">Pour obtenir des instructions détaillées sur la création d’un projet de pages, consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="eea4e-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="eea4e-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-125">Razor Pages</span></span>

<span data-ttu-id="eea4e-126">Razor Pages est activée dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="eea4e-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="eea4e-127">Considérez une page de base : <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="eea4e-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="eea4e-128">Le code précédent ressemble beaucoup à un [ Razor fichier de vue](xref:tutorials/first-mvc-app/adding-view) utilisé dans une application ASP.net core avec des contrôleurs et des vues.</span><span class="sxs-lookup"><span data-stu-id="eea4e-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="eea4e-129">Ce qui le rend différent est la [`@page`](xref:mvc/views/razor#page) directive.</span><span class="sxs-lookup"><span data-stu-id="eea4e-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="eea4e-130">`@page` fait du fichier une action MVC, ce qui signifie qu’il gère les demandes directement, sans passer par un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="eea4e-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="eea4e-131">`@page` doit être la première Razor directive sur une page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="eea4e-132">`@page` affecte le comportement d’autres [Razor](xref:mvc/views/razor) constructions.</span><span class="sxs-lookup"><span data-stu-id="eea4e-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="eea4e-133">Razor Les noms de fichiers de pages ont un suffixe *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eea4e-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="eea4e-134">Une page similaire, utilisant une classe `PageModel`, est illustrée dans les deux fichiers suivants.</span><span class="sxs-lookup"><span data-stu-id="eea4e-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="eea4e-135">Le fichier *Pages/Index2.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="eea4e-136">Le modèle de page *Pages/Index2.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="eea4e-137">Par Convention, le `PageModel` fichier de classe a le même nom que le Razor fichier d’échange avec *. cs* ajouté.</span><span class="sxs-lookup"><span data-stu-id="eea4e-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="eea4e-138">Par exemple, la Razor page précédente est *pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="eea4e-139">Le fichier contenant la classe `PageModel` se nomme *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="eea4e-140">Les associations des chemins d’URL aux pages sont déterminées par l’emplacement de la page dans le système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="eea4e-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="eea4e-141">Le tableau suivant indique un Razor chemin d’accès à la page et l’URL correspondante :</span><span class="sxs-lookup"><span data-stu-id="eea4e-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="eea4e-142">Nom et chemin de fichier</span><span class="sxs-lookup"><span data-stu-id="eea4e-142">File name and path</span></span>               | <span data-ttu-id="eea4e-143">URL correspondante</span><span class="sxs-lookup"><span data-stu-id="eea4e-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eea4e-144">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="eea4e-145">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="eea4e-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="eea4e-146">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="eea4e-147">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="eea4e-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="eea4e-149">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="eea4e-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="eea4e-150">Remarques :</span><span class="sxs-lookup"><span data-stu-id="eea4e-150">Notes:</span></span>

* <span data-ttu-id="eea4e-151">Le runtime recherche les Razor fichiers de pages dans le dossier *pages* par défaut.</span><span class="sxs-lookup"><span data-stu-id="eea4e-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="eea4e-152">`Index` est la page par défaut quand une URL n’inclut pas de page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="eea4e-153">Écrire un formulaire de base</span><span class="sxs-lookup"><span data-stu-id="eea4e-153">Write a basic form</span></span>

<span data-ttu-id="eea4e-154">Razor Les pages sont conçues pour rendre les modèles courants utilisés avec les navigateurs Web faciles à implémenter lors de la création d’une application.</span><span class="sxs-lookup"><span data-stu-id="eea4e-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="eea4e-155">La [liaison de modèle](xref:mvc/models/model-binding), les [tag helpers](xref:mvc/views/tag-helpers/intro)et les applications auxiliaires HTML *fonctionnent tout simplement* avec les propriétés définies dans une classe de Razor page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="eea4e-156">Considérez une page qui implémente un formulaire « Nous contacter » de base pour le modèle `Contact` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="eea4e-157">Pour les exemples de ce document, le `DbContext` est initialisé dans le fichier [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24).</span><span class="sxs-lookup"><span data-stu-id="eea4e-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="eea4e-158">La base de données en mémoire requiert le `Microsoft.EntityFrameworkCore.InMemory` package NuGet.</span><span class="sxs-lookup"><span data-stu-id="eea4e-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="eea4e-159">Le modèle de données :</span><span class="sxs-lookup"><span data-stu-id="eea4e-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="eea4e-160">Le contexte de la base de données :</span><span class="sxs-lookup"><span data-stu-id="eea4e-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="eea4e-161">Le fichier vue *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="eea4e-162">Le modèle de page *Pages/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="eea4e-163">Par convention, la classe `PageModel` se nomme `<PageName>Model` et se trouve dans le même espace de noms que la page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="eea4e-164">La classe `PageModel` permet de séparer la logique d’une page de sa présentation.</span><span class="sxs-lookup"><span data-stu-id="eea4e-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="eea4e-165">Elle définit des gestionnaires de page pour les demandes envoyées à la page et les données utilisées pour l’afficher.</span><span class="sxs-lookup"><span data-stu-id="eea4e-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="eea4e-166">Cette séparation permet :</span><span class="sxs-lookup"><span data-stu-id="eea4e-166">This separation allows:</span></span>

* <span data-ttu-id="eea4e-167">Gestion des dépendances de page via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eea4e-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="eea4e-168">Tests unitaires</span><span class="sxs-lookup"><span data-stu-id="eea4e-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="eea4e-169">La page a une  *méthode de gestionnaire*`OnPostAsync`, qui s’exécute sur les requêtes `POST` (quand un utilisateur poste le formulaire).</span><span class="sxs-lookup"><span data-stu-id="eea4e-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="eea4e-170">Les méthodes de gestionnaire pour tout verbe HTTP peuvent être ajoutées.</span><span class="sxs-lookup"><span data-stu-id="eea4e-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="eea4e-171">Les gestionnaires les plus courants sont :</span><span class="sxs-lookup"><span data-stu-id="eea4e-171">The most common handlers are:</span></span>

* <span data-ttu-id="eea4e-172">`OnGet` pour initialiser l’état nécessaire pour la page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="eea4e-173">Dans le code précédent, la `OnGet` méthode affiche la page *CreateModel. cshtml* Razor .</span><span class="sxs-lookup"><span data-stu-id="eea4e-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="eea4e-174">`OnPost` pour gérer les envois de formulaire.</span><span class="sxs-lookup"><span data-stu-id="eea4e-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="eea4e-175">Le suffixe de nommage `Async` est facultatif, mais souvent utilisé par convention pour les fonctions asynchrones.</span><span class="sxs-lookup"><span data-stu-id="eea4e-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="eea4e-176">Le code précédent est normal pour les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="eea4e-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="eea4e-177">Si vous êtes familiarisé avec les applications ASP.NET à l’aide de contrôleurs et de vues :</span><span class="sxs-lookup"><span data-stu-id="eea4e-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="eea4e-178">Le `OnPostAsync` Code de l’exemple précédent ressemble au code de contrôleur classique.</span><span class="sxs-lookup"><span data-stu-id="eea4e-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="eea4e-179">La plupart des primitives MVC, telles que la [liaison de modèle](xref:mvc/models/model-binding), la [validation](xref:mvc/models/validation)et les résultats d’action, fonctionnent de la même manière avec les contrôleurs et les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="eea4e-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="eea4e-180">La méthode `OnPostAsync` précédente :</span><span class="sxs-lookup"><span data-stu-id="eea4e-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="eea4e-181">Le flux de base de `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="eea4e-182">Vérifiez s’il y a des erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="eea4e-182">Check for validation errors.</span></span>

* <span data-ttu-id="eea4e-183">S’il n’y a aucune erreur, enregistrez les données et redirigez.</span><span class="sxs-lookup"><span data-stu-id="eea4e-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="eea4e-184">S’il y a des erreurs, réaffichez la page avec des messages de validation.</span><span class="sxs-lookup"><span data-stu-id="eea4e-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="eea4e-185">Dans de nombreux cas, les erreurs de validation seraient détectées sur le client et jamais envoyées au serveur.</span><span class="sxs-lookup"><span data-stu-id="eea4e-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="eea4e-186">Le fichier vue *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="eea4e-187">Le rendu HTML à partir de *pages/Create. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="eea4e-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="eea4e-188">Dans le code précédent, la publication du formulaire :</span><span class="sxs-lookup"><span data-stu-id="eea4e-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="eea4e-189">Avec des données valides :</span><span class="sxs-lookup"><span data-stu-id="eea4e-189">With valid data:</span></span>

  * <span data-ttu-id="eea4e-190">La `OnPostAsync` méthode de gestionnaire appelle la <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> méthode d’assistance.</span><span class="sxs-lookup"><span data-stu-id="eea4e-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="eea4e-191">`RedirectToPage` retourne une instance de <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span><span class="sxs-lookup"><span data-stu-id="eea4e-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="eea4e-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="eea4e-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="eea4e-193">Est un résultat d’action.</span><span class="sxs-lookup"><span data-stu-id="eea4e-193">Is an action result.</span></span>
    * <span data-ttu-id="eea4e-194">Est semblable à `RedirectToAction` ou `RedirectToRoute` (utilisé dans les contrôleurs et les vues).</span><span class="sxs-lookup"><span data-stu-id="eea4e-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="eea4e-195">Est personnalisé pour les pages.</span><span class="sxs-lookup"><span data-stu-id="eea4e-195">Is customized for pages.</span></span> <span data-ttu-id="eea4e-196">Dans l’exemple précédent, il redirige vers la page Index racine (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="eea4e-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="eea4e-197">`RedirectToPage` est détaillé dans la section [génération d’URL pour les pages](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="eea4e-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="eea4e-198">Avec les erreurs de validation qui sont transmises au serveur :</span><span class="sxs-lookup"><span data-stu-id="eea4e-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="eea4e-199">La `OnPostAsync` méthode de gestionnaire appelle la <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> méthode d’assistance.</span><span class="sxs-lookup"><span data-stu-id="eea4e-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="eea4e-200">`Page` retourne une instance de <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span><span class="sxs-lookup"><span data-stu-id="eea4e-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="eea4e-201">Le retour de `Page` est similaire à la façon dont les actions dans les contrôleurs retournent `View`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="eea4e-202">`PageResult` est le type de retour par défaut pour une méthode de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="eea4e-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="eea4e-203">Une méthode de gestionnaire qui retourne `void` restitue la page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="eea4e-204">Dans l’exemple précédent, la publication du formulaire sans valeur entraîne le renvoi de la valeur false à [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) .</span><span class="sxs-lookup"><span data-stu-id="eea4e-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="eea4e-205">Dans cet exemple, aucune erreur de validation n’est affichée sur le client.</span><span class="sxs-lookup"><span data-stu-id="eea4e-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="eea4e-206">La gestion des erreurs de validation est traitée plus loin dans ce document.</span><span class="sxs-lookup"><span data-stu-id="eea4e-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="eea4e-207">Avec les erreurs de validation détectées par la validation côté client :</span><span class="sxs-lookup"><span data-stu-id="eea4e-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="eea4e-208">Les données ne sont **pas** publiées sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="eea4e-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="eea4e-209">La validation côté client est expliquée plus loin dans ce document.</span><span class="sxs-lookup"><span data-stu-id="eea4e-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="eea4e-210">La `Customer` propriété utilise l' [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribut pour s’abonner à la liaison de modèle :</span><span class="sxs-lookup"><span data-stu-id="eea4e-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="eea4e-211">`[BindProperty]` ne doit **pas** être utilisé sur les modèles contenant des propriétés qui ne doivent pas être modifiées par le client.</span><span class="sxs-lookup"><span data-stu-id="eea4e-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="eea4e-212">Pour plus d’informations, consultez [survalidation](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="eea4e-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="eea4e-213">Razor Les pages, par défaut, lient les propriétés uniquement avec des non- `GET` verbes.</span><span class="sxs-lookup"><span data-stu-id="eea4e-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="eea4e-214">La liaison aux propriétés évite d’avoir à écrire du code pour convertir des données HTTP en type de modèle.</span><span class="sxs-lookup"><span data-stu-id="eea4e-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="eea4e-215">Elle réduit la quantité de code en utilisant la même propriété pour afficher les champs de formulaire (`<input asp-for="Customer.Name">`) et accepter l’entrée.</span><span class="sxs-lookup"><span data-stu-id="eea4e-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="eea4e-216">Examen du fichier de vue *pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="eea4e-217">Dans le code précédent, le [tag Helper d’entrée](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` lie l’élément HTML `<input>` à l' `Customer.Name` expression de modèle.</span><span class="sxs-lookup"><span data-stu-id="eea4e-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="eea4e-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) rend les tag helpers disponibles.</span><span class="sxs-lookup"><span data-stu-id="eea4e-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="eea4e-219">La page d’accueil</span><span class="sxs-lookup"><span data-stu-id="eea4e-219">The home page</span></span>

<span data-ttu-id="eea4e-220">*Index. cshtml* est la page d’hébergement :</span><span class="sxs-lookup"><span data-stu-id="eea4e-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="eea4e-221">La classe `PageModel` associée (*Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="eea4e-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="eea4e-222">Le fichier *index. cshtml* contient le balisage suivant :</span><span class="sxs-lookup"><span data-stu-id="eea4e-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="eea4e-223">Le `<a /a>` [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a utilisé l' `asp-route-{value}` attribut pour générer un lien vers la page de modification.</span><span class="sxs-lookup"><span data-stu-id="eea4e-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="eea4e-224">Le lien contient des données d’itinéraire avec l’ID de contact.</span><span class="sxs-lookup"><span data-stu-id="eea4e-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="eea4e-225">Par exemple, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="eea4e-226">Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers.</span><span class="sxs-lookup"><span data-stu-id="eea4e-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="eea4e-227">Le fichier *index. cshtml* contient un balisage pour créer un bouton Supprimer pour chaque contact client :</span><span class="sxs-lookup"><span data-stu-id="eea4e-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="eea4e-228">HTML rendu :</span><span class="sxs-lookup"><span data-stu-id="eea4e-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="eea4e-229">Lorsque le bouton supprimer est rendu en HTML, son [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) comprend des paramètres pour :</span><span class="sxs-lookup"><span data-stu-id="eea4e-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="eea4e-230">ID du contact client, spécifié par l' `asp-route-id` attribut.</span><span class="sxs-lookup"><span data-stu-id="eea4e-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="eea4e-231">`handler`, Spécifié par l' `asp-page-handler` attribut.</span><span class="sxs-lookup"><span data-stu-id="eea4e-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="eea4e-232">Quand le bouton est sélectionné, une demande `POST` de forumaire est envoyée au serveur.</span><span class="sxs-lookup"><span data-stu-id="eea4e-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="eea4e-233">Par convention, le nom de la méthode de gestionnaire est sélectionné en fonction de la valeur du paramètre `handler` conformément au schéma `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="eea4e-234">Étant donné que le `handler` est `delete` dans cet exemple, la méthode de gestionnaire `OnPostDeleteAsync` est utilisée pour traiter la demande `POST`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="eea4e-235">Si `asp-page-handler` est défini sur une autre valeur, comme `remove`, une méthode de gestionnaire avec le nom `OnPostRemoveAsync` est sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="eea4e-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="eea4e-236">La méthode `OnPostDeleteAsync` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="eea4e-237">Obtient le `id` de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="eea4e-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="eea4e-238">Interroge la base de données pour le contact client avec `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="eea4e-239">Si le contact client est trouvé, il est supprimé et la base de données est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="eea4e-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="eea4e-240">Appelle <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> pour rediriger vers la page Index racine (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="eea4e-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="eea4e-241">Le fichier Edit. cshtml</span><span class="sxs-lookup"><span data-stu-id="eea4e-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="eea4e-242">La première ligne contient la directive `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="eea4e-243">La contrainte de routage `"{id:int}"` indique à la page qu’elle doit accepter les requêtes qui contiennent des données d’itinéraire `int`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="eea4e-244">Si une requête à la page ne contient de données d’itinéraire qui peuvent être converties en `int`, le runtime retourne une erreur HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="eea4e-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="eea4e-245">Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="eea4e-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="eea4e-246">Le fichier *Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="eea4e-247">Validation</span><span class="sxs-lookup"><span data-stu-id="eea4e-247">Validation</span></span>

<span data-ttu-id="eea4e-248">Règles de validation :</span><span class="sxs-lookup"><span data-stu-id="eea4e-248">Validation rules:</span></span>

* <span data-ttu-id="eea4e-249">Sont spécifiés de façon déclarative dans la classe de modèle.</span><span class="sxs-lookup"><span data-stu-id="eea4e-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="eea4e-250">Sont appliquées partout dans l’application.</span><span class="sxs-lookup"><span data-stu-id="eea4e-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="eea4e-251">L' <xref:System.ComponentModel.DataAnnotations> espace de noms fournit un jeu d’attributs de validation intégrés qui sont appliqués de façon déclarative à une classe ou une propriété.</span><span class="sxs-lookup"><span data-stu-id="eea4e-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="eea4e-252">DataAnnotations contient également des attributs de mise en forme tels [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) que l’aide à la mise en forme et ne fournissent aucune validation.</span><span class="sxs-lookup"><span data-stu-id="eea4e-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="eea4e-253">Prenons le `Customer` modèle :</span><span class="sxs-lookup"><span data-stu-id="eea4e-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="eea4e-254">À l’aide du fichier de vue *Create. cshtml* suivant :</span><span class="sxs-lookup"><span data-stu-id="eea4e-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="eea4e-255">Le code précédent :</span><span class="sxs-lookup"><span data-stu-id="eea4e-255">The preceding code:</span></span>

* <span data-ttu-id="eea4e-256">Comprend des scripts de validation jQuery et jQuery.</span><span class="sxs-lookup"><span data-stu-id="eea4e-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="eea4e-257">Utilise le `<div />` et les `<span />` [aide pour les balises](xref:mvc/views/tag-helpers/intro) pour activer :</span><span class="sxs-lookup"><span data-stu-id="eea4e-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="eea4e-258">Validation côté client.</span><span class="sxs-lookup"><span data-stu-id="eea4e-258">Client-side validation.</span></span>
  * <span data-ttu-id="eea4e-259">Rendu des erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="eea4e-259">Validation error rendering.</span></span>

* <span data-ttu-id="eea4e-260">Génère le code HTML suivant :</span><span class="sxs-lookup"><span data-stu-id="eea4e-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="eea4e-261">La publication de la valeur créer un formulaire sans nom affiche le message d’erreur « le champ nom est obligatoire. »</span><span class="sxs-lookup"><span data-stu-id="eea4e-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="eea4e-262">sur le formulaire.</span><span class="sxs-lookup"><span data-stu-id="eea4e-262">on the form.</span></span> <span data-ttu-id="eea4e-263">Si JavaScript est activé sur le client, le navigateur affiche l’erreur sans la publier sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="eea4e-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="eea4e-264">L' `[StringLength(10)]` attribut génère `data-val-length-max="10"` sur le rendu HTML.</span><span class="sxs-lookup"><span data-stu-id="eea4e-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="eea4e-265">`data-val-length-max` empêche les navigateurs d’entrer une valeur supérieure à la longueur maximale spécifiée.</span><span class="sxs-lookup"><span data-stu-id="eea4e-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="eea4e-266">Si un outil tel que [Fiddler](https://www.telerik.com/fiddler) est utilisé pour modifier et relire la publication :</span><span class="sxs-lookup"><span data-stu-id="eea4e-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="eea4e-267">Avec le nom plus long que 10.</span><span class="sxs-lookup"><span data-stu-id="eea4e-267">With the name longer than 10.</span></span>
* <span data-ttu-id="eea4e-268">Le message d’erreur « le nom du champ doit être une chaîne d’une longueur maximale de 10 ».</span><span class="sxs-lookup"><span data-stu-id="eea4e-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="eea4e-269">» est renvoyé.</span><span class="sxs-lookup"><span data-stu-id="eea4e-269">is returned.</span></span>

<span data-ttu-id="eea4e-270">Prenons le `Movie` modèle suivant :</span><span class="sxs-lookup"><span data-stu-id="eea4e-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="eea4e-271">Les attributs de validation spécifient le comportement à appliquer sur les propriétés de modèle auxquelles ils sont appliqués :</span><span class="sxs-lookup"><span data-stu-id="eea4e-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="eea4e-272">Les `Required` `MinimumLength` attributs et indiquent qu’une propriété doit avoir une valeur, mais rien n’empêche un utilisateur d’entrer un espace blanc pour satisfaire cette validation.</span><span class="sxs-lookup"><span data-stu-id="eea4e-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="eea4e-273">L’attribut `RegularExpression` sert à limiter les caractères pouvant être entrés.</span><span class="sxs-lookup"><span data-stu-id="eea4e-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="eea4e-274">Dans le code précédent, « Genre » :</span><span class="sxs-lookup"><span data-stu-id="eea4e-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="eea4e-275">Doit utiliser seulement des lettres.</span><span class="sxs-lookup"><span data-stu-id="eea4e-275">Must only use letters.</span></span>
  * <span data-ttu-id="eea4e-276">La première lettre doit être une majuscule.</span><span class="sxs-lookup"><span data-stu-id="eea4e-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="eea4e-277">Les espaces, les chiffres et les caractères spéciaux ne sont pas autorisés.</span><span class="sxs-lookup"><span data-stu-id="eea4e-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="eea4e-278">L’expression `RegularExpression` « Rating » :</span><span class="sxs-lookup"><span data-stu-id="eea4e-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="eea4e-279">Nécessite que le premier caractère soit une lettre majuscule.</span><span class="sxs-lookup"><span data-stu-id="eea4e-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="eea4e-280">Autorise les caractères spéciaux et les nombres dans les espaces suivants.</span><span class="sxs-lookup"><span data-stu-id="eea4e-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="eea4e-281">« PG-13 » est valide pour une évaluation, mais échoue pour un « Genre ».</span><span class="sxs-lookup"><span data-stu-id="eea4e-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="eea4e-282">L’attribut `Range` limite une valeur à une plage spécifiée.</span><span class="sxs-lookup"><span data-stu-id="eea4e-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="eea4e-283">L' `StringLength` attribut définit la longueur maximale d’une propriété de type chaîne et, éventuellement, sa longueur minimale.</span><span class="sxs-lookup"><span data-stu-id="eea4e-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="eea4e-284">Les types valeur (tels que `decimal`, `int`, `float` et `DateTime`) sont obligatoires par nature et n’ont pas besoin de l’attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="eea4e-285">La page créer du modèle affiche des `Movie` Erreurs avec des valeurs non valides :</span><span class="sxs-lookup"><span data-stu-id="eea4e-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Formulaire de vue Movie avec plusieurs erreurs de validation jQuery côté client](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="eea4e-287">Pour plus d’informations, consultez :</span><span class="sxs-lookup"><span data-stu-id="eea4e-287">For more information, see:</span></span>

* [<span data-ttu-id="eea4e-288">Ajouter la validation à l’application vidéo</span><span class="sxs-lookup"><span data-stu-id="eea4e-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="eea4e-289">[Validation de modèle dans ASP.net Core](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="eea4e-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="eea4e-290">Gérer les requêtes HEAD avec un gestionnaire OnGet de secours</span><span class="sxs-lookup"><span data-stu-id="eea4e-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="eea4e-291">`HEAD` les requêtes permettent de récupérer les en-têtes pour une ressource spécifique.</span><span class="sxs-lookup"><span data-stu-id="eea4e-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="eea4e-292">Contrairement aux requêtes `GET`, les requêtes `HEAD` ne retournent pas un corps de réponse.</span><span class="sxs-lookup"><span data-stu-id="eea4e-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="eea4e-293">En règle générale, un gestionnaire `OnHead` est créé et appelé pour les requêtes `HEAD` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="eea4e-294">Razor Les pages reviennent à appeler le `OnGet` Gestionnaire si aucun `OnHead` gestionnaire n’est défini.</span><span class="sxs-lookup"><span data-stu-id="eea4e-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="eea4e-295">XSRF/CSRF et Razor pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="eea4e-296">Razor Les pages sont protégées par la validation anti- [contrefaçon](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="eea4e-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="eea4e-297">Le [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injecte des jetons anti-contrefaçon dans des éléments de formulaire HTML.</span><span class="sxs-lookup"><span data-stu-id="eea4e-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="eea4e-298">Utilisation de dispositions, de partiels, de modèles et d’aide pour les balises avec des Razor pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="eea4e-299">Les pages fonctionnent avec toutes les fonctionnalités du Razor moteur d’affichage.</span><span class="sxs-lookup"><span data-stu-id="eea4e-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="eea4e-300">Les mises en page, les partiels, les modèles, les tag helpers, *_ViewStart. cshtml* et *_ViewImports. cshtml* fonctionnent de la même façon que pour les Razor vues conventionnelles.</span><span class="sxs-lookup"><span data-stu-id="eea4e-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="eea4e-301">Nous allons nettoyer un peu cette page en tirant parti de certaines de ces fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="eea4e-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="eea4e-302">Ajoutez une [page de disposition](xref:mvc/views/layout) à *Pages/Shared/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="eea4e-303">La [disposition](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="eea4e-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="eea4e-304">Contrôle la disposition de chaque page (à moins que la page ne refuse la disposition).</span><span class="sxs-lookup"><span data-stu-id="eea4e-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="eea4e-305">Importe des structures HTML telles que JavaScript et les feuilles de style.</span><span class="sxs-lookup"><span data-stu-id="eea4e-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="eea4e-306">Le contenu de la Razor page est rendu où `@RenderBody()` est appelé.</span><span class="sxs-lookup"><span data-stu-id="eea4e-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="eea4e-307">Pour plus d’informations, consultez [page disposition](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="eea4e-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="eea4e-308">La propriété [Layout](xref:mvc/views/layout#specifying-a-layout) est définie dans *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="eea4e-309">La disposition est dans le dossier *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="eea4e-310">Les pages recherchent d’autres vues (dispositions, modèles, partiels) hiérarchiquement, en commençant dans le même dossier que la page active.</span><span class="sxs-lookup"><span data-stu-id="eea4e-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="eea4e-311">Une mise en page dans le dossier *pages/Shared* peut être utilisée à partir de n’importe quelle Razor page sous le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="eea4e-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="eea4e-312">Le fichier de disposition doit être placé dans le dossier *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="eea4e-313">Nous vous recommandons de **ne pas** placer le fichier de disposition dans le dossier *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="eea4e-314">*Views/Shared* est un modèle de vues MVC.</span><span class="sxs-lookup"><span data-stu-id="eea4e-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="eea4e-315">Razor Les pages sont conçues pour s’appuyer sur la hiérarchie des dossiers, et non sur les conventions de chemin.</span><span class="sxs-lookup"><span data-stu-id="eea4e-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="eea4e-316">L’affichage de la recherche à partir d’une Razor page comprend le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="eea4e-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="eea4e-317">Les mises en page, les modèles et les partiels utilisés avec les contrôleurs MVC et les Razor vues conventionnelles *fonctionnent simplement*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="eea4e-318">Ajoutez un fichier *Pages/_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="eea4e-319">`@namespace` est expliqué plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="eea4e-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="eea4e-320">La directive `@addTagHelper` permet de bénéficier des [Tag Helpers intégrés](xref:mvc/views/tag-helpers/builtin-th/Index) dans toutes les pages du dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="eea4e-321">La `@namespace` directive est définie sur une page :</span><span class="sxs-lookup"><span data-stu-id="eea4e-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="eea4e-322">La `@namespace` directive définit l’espace de noms de la page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="eea4e-323">La directive `@model` n’a pas besoin d’inclure l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="eea4e-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="eea4e-324">Quand la directive `@namespace` est contenue dans *_ViewImports.cshtml*, l’espace de noms spécifié fournit le préfixe de l’espace de noms généré dans la Page qui importe la directive `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="eea4e-325">Le reste de l’espace de noms généré (la partie suffixe) est le chemin relatif séparé par un point entre le dossier contenant *_ViewImports.cshtml* et le dossier contenant la page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="eea4e-326">Par exemple, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* définit explicitement l’espace de noms :</span><span class="sxs-lookup"><span data-stu-id="eea4e-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="eea4e-327">Le fichier *Pages/_ViewImports.cshtml* définit l’espace de noms suivant :</span><span class="sxs-lookup"><span data-stu-id="eea4e-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="eea4e-328">L’espace de noms généré pour la page *pages/Customers/Edit. cshtml* Razor est le même que la `PageModel` classe.</span><span class="sxs-lookup"><span data-stu-id="eea4e-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="eea4e-329">`@namespace`*fonctionne également avec les Razor vues conventionnelles.*</span><span class="sxs-lookup"><span data-stu-id="eea4e-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="eea4e-330">Examinez le fichier de vue *pages/Create. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="eea4e-331">Le fichier de vue *pages/Create. cshtml* mis à jour avec *_ViewImports. cshtml* et le fichier de disposition précédent :</span><span class="sxs-lookup"><span data-stu-id="eea4e-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="eea4e-332">Dans le code précédent, le *_ViewImports. cshtml* a importé l’espace de noms et les tag helpers.</span><span class="sxs-lookup"><span data-stu-id="eea4e-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="eea4e-333">Le fichier de disposition a importé les fichiers JavaScript.</span><span class="sxs-lookup"><span data-stu-id="eea4e-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="eea4e-334">Le [ Razor projet de démarrage pages](#rpvs17) contient les *pages/_ValidationScriptsPartial. cshtml*, qui raccorde la validation côté client.</span><span class="sxs-lookup"><span data-stu-id="eea4e-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="eea4e-335">Pour plus d'informations sur les affichages partiels, consultez <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="eea4e-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="eea4e-336">Génération d’URL pour les pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-336">URL generation for Pages</span></span>

<span data-ttu-id="eea4e-337">La page `Create`, illustrée précédemment, utilise `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="eea4e-338">L’application a la structure de fichiers/dossiers suivante :</span><span class="sxs-lookup"><span data-stu-id="eea4e-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="eea4e-339">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="eea4e-339">*/Pages*</span></span>

  * <span data-ttu-id="eea4e-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="eea4e-341">*Privacy. cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="eea4e-342">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="eea4e-342">*/Customers*</span></span>

    * <span data-ttu-id="eea4e-343">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="eea4e-344">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="eea4e-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-345">*Index.cshtml*</span></span>

<span data-ttu-id="eea4e-346">Les pages *pages/Customers/Create. cshtml* et *pages/Customers/Edit. cshtml* redirigent vers *pages/Customers/index. cshtml* après réussite.</span><span class="sxs-lookup"><span data-stu-id="eea4e-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="eea4e-347">La chaîne `./Index` est un nom de page relatif utilisé pour accéder à la page précédente.</span><span class="sxs-lookup"><span data-stu-id="eea4e-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="eea4e-348">Elle est utilisée pour générer des URL dans la page *pages/Customers/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eea4e-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="eea4e-349">Exemple :</span><span class="sxs-lookup"><span data-stu-id="eea4e-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="eea4e-350">Le nom de page absolu `/Index` est utilisé pour générer des URL dans la page *pages/index. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="eea4e-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="eea4e-351">Exemple :</span><span class="sxs-lookup"><span data-stu-id="eea4e-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="eea4e-352">Le nom de la page est le chemin de la page à partir du dossier racine */Pages* avec un `/` devant (par exemple, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="eea4e-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="eea4e-353">Les exemples de génération d’URL précédents offrent des options améliorées et des fonctionnalités fonctionnelles par rapport au codage en dur d’une URL.</span><span class="sxs-lookup"><span data-stu-id="eea4e-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="eea4e-354">La génération d’URL utilise le [routage](xref:mvc/controllers/routing) et peut générer et encoder des paramètres en fonction de la façon dont l’itinéraire est défini dans le chemin de destination.</span><span class="sxs-lookup"><span data-stu-id="eea4e-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="eea4e-355">La génération d’URL pour les pages prend en charge les noms relatifs.</span><span class="sxs-lookup"><span data-stu-id="eea4e-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="eea4e-356">Le tableau suivant indique quelle page d’index est sélectionnée à l’aide `RedirectToPage` de différents paramètres dans *pages/Customers/Create. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="eea4e-357">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="eea4e-357">RedirectToPage(x)</span></span>| <span data-ttu-id="eea4e-358">Page</span><span class="sxs-lookup"><span data-stu-id="eea4e-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eea4e-359">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="eea4e-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="eea4e-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="eea4e-360">*Pages/Index*</span></span> |
| <span data-ttu-id="eea4e-361">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="eea4e-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="eea4e-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="eea4e-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="eea4e-363">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="eea4e-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="eea4e-364">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="eea4e-364">*Pages/Index*</span></span> |
| <span data-ttu-id="eea4e-365">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="eea4e-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="eea4e-366">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="eea4e-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="eea4e-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")` et `RedirectToPage("../Index")` sont des *noms relatifs*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="eea4e-368">Le paramètre `RedirectToPage` est *combiné* avec le chemin de la page active pour calculer le nom de la page de destination.</span><span class="sxs-lookup"><span data-stu-id="eea4e-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="eea4e-369">La liaison de nom relatif est utile lors de la création de sites avec une structure complexe.</span><span class="sxs-lookup"><span data-stu-id="eea4e-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="eea4e-370">Lorsque des noms relatifs sont utilisés pour établir une liaison entre les pages d’un dossier :</span><span class="sxs-lookup"><span data-stu-id="eea4e-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="eea4e-371">Le changement de nom d’un dossier n’interrompt pas les liens relatifs.</span><span class="sxs-lookup"><span data-stu-id="eea4e-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="eea4e-372">Les liens ne sont pas rompus, car ils n’incluent pas le nom du dossier.</span><span class="sxs-lookup"><span data-stu-id="eea4e-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="eea4e-373">Pour rediriger vers une page située dans une autre [Zone](xref:mvc/controllers/areas), spécifiez la zone :</span><span class="sxs-lookup"><span data-stu-id="eea4e-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="eea4e-374">Pour plus d’informations, consultez <xref:mvc/controllers/areas> et <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="eea4e-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="eea4e-375">Attribut ViewData</span><span class="sxs-lookup"><span data-stu-id="eea4e-375">ViewData attribute</span></span>

<span data-ttu-id="eea4e-376">Les données peuvent être passées à une page avec <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> .</span><span class="sxs-lookup"><span data-stu-id="eea4e-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="eea4e-377">Les valeurs des propriétés avec l' `[ViewData]` attribut sont stockées et chargées à partir de <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> .</span><span class="sxs-lookup"><span data-stu-id="eea4e-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="eea4e-378">Dans l’exemple suivant, le `AboutModel` applique l' `[ViewData]` attribut à la `Title` propriété :</span><span class="sxs-lookup"><span data-stu-id="eea4e-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="eea4e-379">Dans la page À propos de, accédez à la propriété `Title` en tant que propriété de modèle :</span><span class="sxs-lookup"><span data-stu-id="eea4e-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="eea4e-380">Dans la disposition, le titre est lu à partir du dictionnaire ViewData :</span><span class="sxs-lookup"><span data-stu-id="eea4e-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="eea4e-381">TempData</span><span class="sxs-lookup"><span data-stu-id="eea4e-381">TempData</span></span>

<span data-ttu-id="eea4e-382">ASP.NET Core expose <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> .</span><span class="sxs-lookup"><span data-stu-id="eea4e-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="eea4e-383">Cette propriété stocke les données jusqu’à ce qu’elles soient lues.</span><span class="sxs-lookup"><span data-stu-id="eea4e-383">This property stores data until it's read.</span></span> <span data-ttu-id="eea4e-384">Vous pouvez utiliser les méthodes <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> et <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> pour examiner les données sans suppression.</span><span class="sxs-lookup"><span data-stu-id="eea4e-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="eea4e-385">`TempData` est utile pour la redirection, quand des données sont nécessaires pour plusieurs requêtes.</span><span class="sxs-lookup"><span data-stu-id="eea4e-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="eea4e-386">Le code suivant définit la valeur de `Message` à l’aide de `TempData` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="eea4e-387">Le balisage suivant dans le fichier *Pages/Customers/Index.cshtml* affiche la valeur de `Message` à l’aide de `TempData`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="eea4e-388">Le modèle de page *Pages/Customers/Index.cshtml.cs* applique l’attribut `[TempData]` à la propriété `Message`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="eea4e-389">Pour plus d’informations, consultez [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="eea4e-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="eea4e-390">Plusieurs gestionnaires par page</span><span class="sxs-lookup"><span data-stu-id="eea4e-390">Multiple handlers per page</span></span>

<span data-ttu-id="eea4e-391">La page suivante génère un balisage pour deux gestionnaires en utilisant le Tag Helper `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="eea4e-392">Le formulaire dans l’exemple précédent contient deux boutons d’envoi, chacun utilisant `FormActionTagHelper` pour envoyer à une URL différente.</span><span class="sxs-lookup"><span data-stu-id="eea4e-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="eea4e-393">L’attribut `asp-page-handler` est un complément de `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="eea4e-394">`asp-page-handler` génère des URL qui envoient à chacune des méthodes de gestionnaire définies par une page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="eea4e-395">`asp-page` n’est pas spécifié, car l’exemple établit une liaison à la page active.</span><span class="sxs-lookup"><span data-stu-id="eea4e-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="eea4e-396">Le modèle de page :</span><span class="sxs-lookup"><span data-stu-id="eea4e-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="eea4e-397">Le code précédent utilise des *méthodes de gestionnaire nommées*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="eea4e-398">Pour créer des méthodes de gestionnaire nommées, il faut prendre le texte dans le nom après `On<HTTP Verb>` et avant `Async` (le cas échéant).</span><span class="sxs-lookup"><span data-stu-id="eea4e-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="eea4e-399">Dans l’exemple précédent, les méthodes de page sont OnPost **JoinList** Async et OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="eea4e-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="eea4e-400">Avec *OnPost* et *Async* supprimés, les noms des gestionnaires sont `JoinList` et `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="eea4e-401">Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="eea4e-402">Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="eea4e-403">Routes personnalisées</span><span class="sxs-lookup"><span data-stu-id="eea4e-403">Custom routes</span></span>

<span data-ttu-id="eea4e-404">Utilisez la directive `@page` pour :</span><span class="sxs-lookup"><span data-stu-id="eea4e-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="eea4e-405">Spécifier une route personnalisée vers une page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-405">Specify a custom route to a page.</span></span> <span data-ttu-id="eea4e-406">Par exemple, la route vers la page À propos peut être définie sur `/Some/Other/Path` avec `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="eea4e-407">Ajouter des segments à la route par défaut d’une page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-407">Append segments to a page's default route.</span></span> <span data-ttu-id="eea4e-408">Par exemple, un segment « item » peut être ajouté à la route par défaut d’une page avec `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="eea4e-409">Ajouter des paramètres à la route par défaut d’une page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="eea4e-410">Par exemple, un paramètre d’ID, `id`, peut être nécessaire pour une page avec `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="eea4e-411">Un chemin relatif racine désigné par un tilde (`~`) au début du chemin est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="eea4e-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="eea4e-412">Par exemple, `@page "~/Some/Other/Path"` est identique à `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="eea4e-413">Si vous n’aimez pas la chaîne `?handler=JoinList` de requête dans l’URL, modifiez l’itinéraire pour placer le nom du gestionnaire dans la partie chemin d’accès de l’URL.</span><span class="sxs-lookup"><span data-stu-id="eea4e-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="eea4e-414">L’itinéraire peut être personnalisé en ajoutant un modèle de routage entre guillemets doubles après la `@page` directive.</span><span class="sxs-lookup"><span data-stu-id="eea4e-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="eea4e-415">Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="eea4e-416">Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="eea4e-417">Le `?` suivant `handler` signifie que le paramètre d’itinéraire est facultatif.</span><span class="sxs-lookup"><span data-stu-id="eea4e-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="eea4e-418">Configuration et paramètres avancés</span><span class="sxs-lookup"><span data-stu-id="eea4e-418">Advanced configuration and settings</span></span>

<span data-ttu-id="eea4e-419">La configuration et les paramètres des sections suivantes ne sont pas requis par la plupart des applications.</span><span class="sxs-lookup"><span data-stu-id="eea4e-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="eea4e-420">Pour configurer des options avancées, utilisez la surcharge qui est configurée <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> :</span><span class="sxs-lookup"><span data-stu-id="eea4e-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="eea4e-421">Utilisez <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> pour définir le répertoire racine pour les pages ou ajouter des conventions de modèle d’application pour les pages.</span><span class="sxs-lookup"><span data-stu-id="eea4e-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="eea4e-422">Pour plus d’informations sur les conventions, consultez [ Razor conventions d’autorisation des pages](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="eea4e-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="eea4e-423">Pour précompiler des vues, consultez [ Razor afficher la compilation](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="eea4e-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="eea4e-424">Spécifier que Razor les pages se trouvent à la racine du contenu</span><span class="sxs-lookup"><span data-stu-id="eea4e-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="eea4e-425">Par défaut, Razor les pages sont enracinées dans le répertoire */pages*</span><span class="sxs-lookup"><span data-stu-id="eea4e-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="eea4e-426">Ajouter <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> pour spécifier que vos Razor pages se trouvent à la [racine du contenu](xref:fundamentals/index#content-root) ( <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> ) de l’application :</span><span class="sxs-lookup"><span data-stu-id="eea4e-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="eea4e-427">Spécifier que Razor les pages se trouvent dans un répertoire racine personnalisé</span><span class="sxs-lookup"><span data-stu-id="eea4e-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="eea4e-428">Ajouter <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> pour spécifier que Razor les pages se trouvent dans un répertoire racine personnalisé dans l’application (fournissez un chemin d’accès relatif) :</span><span class="sxs-lookup"><span data-stu-id="eea4e-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="eea4e-429">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eea4e-429">Additional resources</span></span>

* <span data-ttu-id="eea4e-430">Consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start), qui s’appuie sur cette introduction.</span><span class="sxs-lookup"><span data-stu-id="eea4e-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="eea4e-431">Autoriser l’attribut et les Razor pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="eea4e-432">Télécharger ou afficher l’exemple de code</span><span class="sxs-lookup"><span data-stu-id="eea4e-432">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* [<span data-ttu-id="eea4e-433">Razor Référence de syntaxe pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eea4e-433">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="eea4e-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eea4e-434">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="eea4e-435">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eea4e-435">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eea4e-436">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="eea4e-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="eea4e-437">Créer un Razor projet pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-437">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eea4e-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eea4e-438">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eea4e-439">Pour obtenir des instructions détaillées sur la création d’un projet de pages, consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start) Razor .</span><span class="sxs-lookup"><span data-stu-id="eea4e-439">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="eea4e-440">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="eea4e-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="eea4e-441">Exécutez `dotnet new webapp` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="eea4e-441">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="eea4e-442">Ouvrez le fichier *.csproj* généré à partir de Visual Studio pour Mac.</span><span class="sxs-lookup"><span data-stu-id="eea4e-442">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eea4e-443">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eea4e-443">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eea4e-444">Exécutez `dotnet new webapp` à partir de la ligne de commande.</span><span class="sxs-lookup"><span data-stu-id="eea4e-444">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="eea4e-445">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-445">Razor Pages</span></span>

<span data-ttu-id="eea4e-446">Razor Pages est activée dans *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="eea4e-446">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="eea4e-447">Considérez une page de base : <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="eea4e-447">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="eea4e-448">Le code précédent ressemble beaucoup à un [ Razor fichier de vue](xref:tutorials/first-mvc-app/adding-view) utilisé dans une application ASP.net core avec des contrôleurs et des vues.</span><span class="sxs-lookup"><span data-stu-id="eea4e-448">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="eea4e-449">Ce qui le rend différent est la directive `@page`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-449">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="eea4e-450">`@page` fait du fichier une action MVC, ce qui signifie qu’il gère les demandes directement, sans passer par un contrôleur.</span><span class="sxs-lookup"><span data-stu-id="eea4e-450">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="eea4e-451">`@page` doit être la première Razor directive sur une page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-451">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="eea4e-452">`@page` affecte le comportement d’autres Razor constructions.</span><span class="sxs-lookup"><span data-stu-id="eea4e-452">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="eea4e-453">Une page similaire, utilisant une classe `PageModel`, est illustrée dans les deux fichiers suivants.</span><span class="sxs-lookup"><span data-stu-id="eea4e-453">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="eea4e-454">Le fichier *Pages/Index2.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-454">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="eea4e-455">Le modèle de page *Pages/Index2.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-455">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="eea4e-456">Par Convention, le `PageModel` fichier de classe a le même nom que le Razor fichier d’échange avec *. cs* ajouté.</span><span class="sxs-lookup"><span data-stu-id="eea4e-456">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="eea4e-457">Par exemple, la Razor page précédente est *pages/index2. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-457">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="eea4e-458">Le fichier contenant la classe `PageModel` se nomme *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-458">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="eea4e-459">Les associations des chemins d’URL aux pages sont déterminées par l’emplacement de la page dans le système de fichiers.</span><span class="sxs-lookup"><span data-stu-id="eea4e-459">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="eea4e-460">Le tableau suivant indique un Razor chemin d’accès à la page et l’URL correspondante :</span><span class="sxs-lookup"><span data-stu-id="eea4e-460">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="eea4e-461">Nom et chemin de fichier</span><span class="sxs-lookup"><span data-stu-id="eea4e-461">File name and path</span></span>               | <span data-ttu-id="eea4e-462">URL correspondante</span><span class="sxs-lookup"><span data-stu-id="eea4e-462">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eea4e-463">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-463">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="eea4e-464">`/` ou `/Index`</span><span class="sxs-lookup"><span data-stu-id="eea4e-464">`/` or `/Index`</span></span> |
| <span data-ttu-id="eea4e-465">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-465">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="eea4e-466">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-466">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="eea4e-467">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-467">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="eea4e-468">`/Store` ou `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="eea4e-468">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="eea4e-469">Remarques :</span><span class="sxs-lookup"><span data-stu-id="eea4e-469">Notes:</span></span>

* <span data-ttu-id="eea4e-470">Le runtime recherche les Razor fichiers de pages dans le dossier *pages* par défaut.</span><span class="sxs-lookup"><span data-stu-id="eea4e-470">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="eea4e-471">`Index` est la page par défaut quand une URL n’inclut pas de page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-471">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="eea4e-472">Écrire un formulaire de base</span><span class="sxs-lookup"><span data-stu-id="eea4e-472">Write a basic form</span></span>

<span data-ttu-id="eea4e-473">Razor Les pages sont conçues pour rendre les modèles courants utilisés avec les navigateurs Web faciles à implémenter lors de la création d’une application.</span><span class="sxs-lookup"><span data-stu-id="eea4e-473">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="eea4e-474">La [liaison de modèle](xref:mvc/models/model-binding), les [tag helpers](xref:mvc/views/tag-helpers/intro)et les applications auxiliaires HTML *fonctionnent tout simplement* avec les propriétés définies dans une classe de Razor page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-474">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="eea4e-475">Considérez une page qui implémente un formulaire « Nous contacter » de base pour le modèle `Contact` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-475">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="eea4e-476">Pour les exemples de ce document, le `DbContext` est initialisé dans le fichier [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16).</span><span class="sxs-lookup"><span data-stu-id="eea4e-476">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="eea4e-477">Le modèle de données :</span><span class="sxs-lookup"><span data-stu-id="eea4e-477">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="eea4e-478">Le contexte de la base de données :</span><span class="sxs-lookup"><span data-stu-id="eea4e-478">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="eea4e-479">Le fichier vue *Pages/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-479">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="eea4e-480">Le modèle de page *Pages/Create.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-480">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="eea4e-481">Par convention, la classe `PageModel` se nomme `<PageName>Model` et se trouve dans le même espace de noms que la page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-481">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="eea4e-482">La classe `PageModel` permet de séparer la logique d’une page de sa présentation.</span><span class="sxs-lookup"><span data-stu-id="eea4e-482">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="eea4e-483">Elle définit des gestionnaires de page pour les demandes envoyées à la page et les données utilisées pour l’afficher.</span><span class="sxs-lookup"><span data-stu-id="eea4e-483">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="eea4e-484">Cette séparation permet :</span><span class="sxs-lookup"><span data-stu-id="eea4e-484">This separation allows:</span></span>

* <span data-ttu-id="eea4e-485">Gestion des dépendances de page via l' [injection de dépendances](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eea4e-485">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="eea4e-486">[Test unitaire](xref:test/razor-pages-tests) des pages.</span><span class="sxs-lookup"><span data-stu-id="eea4e-486">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="eea4e-487">La page a une  *méthode de gestionnaire*`OnPostAsync`, qui s’exécute sur les requêtes `POST` (quand un utilisateur poste le formulaire).</span><span class="sxs-lookup"><span data-stu-id="eea4e-487">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="eea4e-488">Vous pouvez ajouter des méthodes de gestionnaire pour n’importe quel verbe HTTP.</span><span class="sxs-lookup"><span data-stu-id="eea4e-488">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="eea4e-489">Les gestionnaires les plus courants sont :</span><span class="sxs-lookup"><span data-stu-id="eea4e-489">The most common handlers are:</span></span>

* <span data-ttu-id="eea4e-490">`OnGet` pour initialiser l’état nécessaire pour la page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-490">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="eea4e-491">Exemple [OnGet](#OnGet).</span><span class="sxs-lookup"><span data-stu-id="eea4e-491">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="eea4e-492">`OnPost` pour gérer les envois de formulaire.</span><span class="sxs-lookup"><span data-stu-id="eea4e-492">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="eea4e-493">Le suffixe de nommage `Async` est facultatif, mais souvent utilisé par convention pour les fonctions asynchrones.</span><span class="sxs-lookup"><span data-stu-id="eea4e-493">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="eea4e-494">Le code précédent est normal pour les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="eea4e-494">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="eea4e-495">Si vous êtes familiarisé avec les applications ASP.NET à l’aide de contrôleurs et de vues :</span><span class="sxs-lookup"><span data-stu-id="eea4e-495">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="eea4e-496">Le `OnPostAsync` Code de l’exemple précédent ressemble au code de contrôleur classique.</span><span class="sxs-lookup"><span data-stu-id="eea4e-496">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="eea4e-497">La plupart des primitives MVC, telles que la [liaison de modèle](xref:mvc/models/model-binding), la [validation](xref:mvc/models/validation), la [validation](xref:mvc/models/validation)et les résultats d’action, sont partagées.</span><span class="sxs-lookup"><span data-stu-id="eea4e-497">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="eea4e-498">La méthode `OnPostAsync` précédente :</span><span class="sxs-lookup"><span data-stu-id="eea4e-498">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="eea4e-499">Le flux de base de `OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-499">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="eea4e-500">Vérifiez s’il y a des erreurs de validation.</span><span class="sxs-lookup"><span data-stu-id="eea4e-500">Check for validation errors.</span></span>

* <span data-ttu-id="eea4e-501">S’il n’y a aucune erreur, enregistrez les données et redirigez.</span><span class="sxs-lookup"><span data-stu-id="eea4e-501">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="eea4e-502">S’il y a des erreurs, réaffichez la page avec des messages de validation.</span><span class="sxs-lookup"><span data-stu-id="eea4e-502">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="eea4e-503">La validation côté client est identique à celle des applications ASP.NET Core MVC traditionnelles.</span><span class="sxs-lookup"><span data-stu-id="eea4e-503">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="eea4e-504">Dans de nombreux cas, les erreurs de validation seraient détectées sur le client et jamais envoyées au serveur.</span><span class="sxs-lookup"><span data-stu-id="eea4e-504">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="eea4e-505">Quand les données sont entrées correctement, la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `RedirectToPage` pour retourner une instance de `RedirectToPageResult`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-505">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="eea4e-506">`RedirectToPage` est un nouveau résultat d’action, semblable à `RedirectToAction` ou `RedirectToRoute`, mais personnalisé pour les pages.</span><span class="sxs-lookup"><span data-stu-id="eea4e-506">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="eea4e-507">Dans l’exemple précédent, il redirige vers la page Index racine (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="eea4e-507">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="eea4e-508">`RedirectToPage` est détaillé dans la section [génération d’URL pour les pages](#url_gen) .</span><span class="sxs-lookup"><span data-stu-id="eea4e-508">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="eea4e-509">Quand le formulaire envoyé comporte des erreurs de validation (qui sont passées au serveur), la méthode de gestionnaire `OnPostAsync` appelle la méthode d’assistance `Page`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-509">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="eea4e-510">`Page` retourne une instance de `PageResult`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-510">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="eea4e-511">Le retour de `Page` est similaire à la façon dont les actions dans les contrôleurs retournent `View`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-511">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="eea4e-512">`PageResult` est le type de retour par défaut pour une méthode de gestionnaire.</span><span class="sxs-lookup"><span data-stu-id="eea4e-512">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="eea4e-513">Une méthode de gestionnaire qui retourne `void` restitue la page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-513">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="eea4e-514">La propriété `Customer` utilise l’attribut `[BindProperty]` pour accepter la liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="eea4e-514">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="eea4e-515">Razor Les pages, par défaut, lient les propriétés uniquement avec des non- `GET` verbes.</span><span class="sxs-lookup"><span data-stu-id="eea4e-515">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="eea4e-516">La liaison à des propriétés peut réduire la quantité de code à écrire.</span><span class="sxs-lookup"><span data-stu-id="eea4e-516">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="eea4e-517">Elle réduit la quantité de code en utilisant la même propriété pour afficher les champs de formulaire (`<input asp-for="Customer.Name">`) et accepter l’entrée.</span><span class="sxs-lookup"><span data-stu-id="eea4e-517">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="eea4e-518">La page d’accueil (*Index.cshtml*) :</span><span class="sxs-lookup"><span data-stu-id="eea4e-518">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="eea4e-519">La classe `PageModel` associée (*Index.cshtml.cs*) :</span><span class="sxs-lookup"><span data-stu-id="eea4e-519">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="eea4e-520">Le fichier *Index.cshtml* contient le balisage suivant pour créer un lien d’édition pour chaque contact :</span><span class="sxs-lookup"><span data-stu-id="eea4e-520">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="eea4e-521">Le `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [tag Helper ancre](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a utilisé l' `asp-route-{value}` attribut pour générer un lien vers la page de modification.</span><span class="sxs-lookup"><span data-stu-id="eea4e-521">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="eea4e-522">Le lien contient des données d’itinéraire avec l’ID de contact.</span><span class="sxs-lookup"><span data-stu-id="eea4e-522">The link contains route data with the contact ID.</span></span> <span data-ttu-id="eea4e-523">Par exemple, `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-523">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="eea4e-524">Les [tag helpers](xref:mvc/views/tag-helpers/intro) permettent au code côté serveur de participer à la création et au rendu des éléments HTML dans les Razor fichiers.</span><span class="sxs-lookup"><span data-stu-id="eea4e-524">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="eea4e-525">Les Tag Helpers sont activés par `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="eea4e-525">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="eea4e-526">Le fichier *Pages/Edit.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-526">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="eea4e-527">La première ligne contient la directive `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-527">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="eea4e-528">La contrainte de routage `"{id:int}"` indique à la page qu’elle doit accepter les requêtes qui contiennent des données d’itinéraire `int`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-528">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="eea4e-529">Si une requête à la page ne contient de données d’itinéraire qui peuvent être converties en `int`, le runtime retourne une erreur HTTP 404 (introuvable).</span><span class="sxs-lookup"><span data-stu-id="eea4e-529">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="eea4e-530">Pour que l’ID soit facultatif, ajoutez `?` à la contrainte d’itinéraire :</span><span class="sxs-lookup"><span data-stu-id="eea4e-530">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="eea4e-531">Le fichier *Pages/Edit.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-531">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="eea4e-532">Le fichier *Index.cshtml* contient également le balisage pour créer un bouton Supprimer pour chaque contact client :</span><span class="sxs-lookup"><span data-stu-id="eea4e-532">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="eea4e-533">Lorsque le bouton Supprimer est rendu en HTML, son `formaction` comprend des paramètres pour :</span><span class="sxs-lookup"><span data-stu-id="eea4e-533">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="eea4e-534">L’ID du contact client spécifié par l’attribut `asp-route-id`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-534">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="eea4e-535">Le `handler` spécifié par l’attribut `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-535">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="eea4e-536">Voici un exemple de bouton Supprimer rendu avec un ID de contact client de `1`:</span><span class="sxs-lookup"><span data-stu-id="eea4e-536">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="eea4e-537">Quand le bouton est sélectionné, une demande `POST` de forumaire est envoyée au serveur.</span><span class="sxs-lookup"><span data-stu-id="eea4e-537">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="eea4e-538">Par convention, le nom de la méthode de gestionnaire est sélectionné en fonction de la valeur du paramètre `handler` conformément au schéma `OnPost[handler]Async`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-538">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="eea4e-539">Étant donné que le `handler` est `delete` dans cet exemple, la méthode de gestionnaire `OnPostDeleteAsync` est utilisée pour traiter la demande `POST`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-539">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="eea4e-540">Si `asp-page-handler` est défini sur une autre valeur, comme `remove`, une méthode de gestionnaire avec le nom `OnPostRemoveAsync` est sélectionnée.</span><span class="sxs-lookup"><span data-stu-id="eea4e-540">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="eea4e-541">Le code suivant illustre le `OnPostDeleteAsync` Gestionnaire :</span><span class="sxs-lookup"><span data-stu-id="eea4e-541">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="eea4e-542">La méthode `OnPostDeleteAsync` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-542">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="eea4e-543">Accepte l’`id` de la chaîne de requête.</span><span class="sxs-lookup"><span data-stu-id="eea4e-543">Accepts the `id` from the query string.</span></span> <span data-ttu-id="eea4e-544">Si la directive de la page *index. cshtml* contenait la contrainte `"{id:int?}"` de routage, `id` provient des données d’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="eea4e-544">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="eea4e-545">Les données d’itinéraire pour `id` sont spécifiées dans l’URI, par exemple `https://localhost:5001/Customers/2` .</span><span class="sxs-lookup"><span data-stu-id="eea4e-545">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="eea4e-546">Interroge la base de données pour le contact client avec `FindAsync`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-546">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="eea4e-547">Si le contact client est trouvé, il est supprimé de la liste des contacts client.</span><span class="sxs-lookup"><span data-stu-id="eea4e-547">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="eea4e-548">La base de données est mise à jour.</span><span class="sxs-lookup"><span data-stu-id="eea4e-548">The database is updated.</span></span>
* <span data-ttu-id="eea4e-549">Appelle `RedirectToPage` pour rediriger vers la page Index racine (`/Index`).</span><span class="sxs-lookup"><span data-stu-id="eea4e-549">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="eea4e-550">Marquer les propriétés de page comme Required</span><span class="sxs-lookup"><span data-stu-id="eea4e-550">Mark page properties as required</span></span>

<span data-ttu-id="eea4e-551">Les propriétés d’un `PageModel` peuvent être marquées avec l’attribut [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) :</span><span class="sxs-lookup"><span data-stu-id="eea4e-551">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="eea4e-552">Pour plus d’informations, consultez [Validation de modèle](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="eea4e-552">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="eea4e-553">Gérer les requêtes HEAD avec un gestionnaire OnGet de secours</span><span class="sxs-lookup"><span data-stu-id="eea4e-553">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="eea4e-554">Les requêtes `HEAD` vous permettent de récupérer les en-têtes pour une ressource spécifique.</span><span class="sxs-lookup"><span data-stu-id="eea4e-554">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="eea4e-555">Contrairement aux requêtes `GET`, les requêtes `HEAD` ne retournent pas un corps de réponse.</span><span class="sxs-lookup"><span data-stu-id="eea4e-555">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="eea4e-556">En règle générale, un gestionnaire `OnHead` est créé et appelé pour les requêtes `HEAD` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-556">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="eea4e-557">Dans ASP.NET Core 2,1 ou version ultérieure, Razor les pages reviennent à appeler le `OnGet` Gestionnaire si aucun `OnHead` gestionnaire n’est défini.</span><span class="sxs-lookup"><span data-stu-id="eea4e-557">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="eea4e-558">Ce comportement est activé par l’appel à [SetCompatibilityVersion](xref:mvc/compatibility-version) dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-558">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="eea4e-559">Les modèles par défaut génèrent l'appel `SetCompatibilityVersion` dans ASP.NET Core 2.1 et 2.2.</span><span class="sxs-lookup"><span data-stu-id="eea4e-559">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="eea4e-560">`SetCompatibilityVersion` affecte efficacement Razor à l’option pages la `AllowMappingHeadRequestsToGetHandler` valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="eea4e-560">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="eea4e-561">Au lieu d’adhérer à tous les comportements avec `SetCompatibilityVersion`, vous pouvez adhérer explicitement à des comportements *spécifiques*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-561">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="eea4e-562">Le code suivant adhère à l’autorisation de mappage des requêtes `HEAD` au gestionnaire `OnGet` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-562">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="eea4e-563">XSRF/CSRF et Razor pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-563">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="eea4e-564">Vous n’avez aucun code à écrire pour la [validation anti-contrefaçon](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="eea4e-564">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="eea4e-565">La génération et la validation des jetons anti-contrefaçon sont automatiquement incluses dans les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="eea4e-565">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="eea4e-566">Utilisation de dispositions, de partiels, de modèles et d’aide pour les balises avec des Razor pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-566">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="eea4e-567">Les pages fonctionnent avec toutes les fonctionnalités du Razor moteur d’affichage.</span><span class="sxs-lookup"><span data-stu-id="eea4e-567">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="eea4e-568">Les mises en page, les partiels, les modèles, les tag helpers, *_ViewStart. cshtml*, *_ViewImports. cshtml* fonctionnent de la même façon que pour les Razor vues conventionnelles.</span><span class="sxs-lookup"><span data-stu-id="eea4e-568">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="eea4e-569">Nous allons nettoyer un peu cette page en tirant parti de certaines de ces fonctionnalités.</span><span class="sxs-lookup"><span data-stu-id="eea4e-569">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="eea4e-570">Ajoutez une [page de disposition](xref:mvc/views/layout) à *Pages/Shared/_Layout.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-570">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="eea4e-571">La [disposition](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="eea4e-571">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="eea4e-572">Contrôle la disposition de chaque page (à moins que la page ne refuse la disposition).</span><span class="sxs-lookup"><span data-stu-id="eea4e-572">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="eea4e-573">Importe des structures HTML telles que JavaScript et les feuilles de style.</span><span class="sxs-lookup"><span data-stu-id="eea4e-573">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="eea4e-574">Pour plus d’informations, consultez [Page de disposition](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="eea4e-574">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="eea4e-575">La propriété [Layout](xref:mvc/views/layout#specifying-a-layout) est définie dans *Pages/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-575">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="eea4e-576">La disposition est dans le dossier *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-576">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="eea4e-577">Les pages recherchent d’autres vues (dispositions, modèles, partiels) hiérarchiquement, en commençant dans le même dossier que la page active.</span><span class="sxs-lookup"><span data-stu-id="eea4e-577">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="eea4e-578">Une mise en page dans le dossier *pages/Shared* peut être utilisée à partir de n’importe quelle Razor page sous le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="eea4e-578">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="eea4e-579">Le fichier de disposition doit être placé dans le dossier *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-579">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="eea4e-580">Nous vous recommandons de **ne pas** placer le fichier de disposition dans le dossier *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-580">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="eea4e-581">*Views/Shared* est un modèle de vues MVC.</span><span class="sxs-lookup"><span data-stu-id="eea4e-581">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="eea4e-582">Razor Les pages sont conçues pour s’appuyer sur la hiérarchie des dossiers, et non sur les conventions de chemin.</span><span class="sxs-lookup"><span data-stu-id="eea4e-582">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="eea4e-583">L’affichage de la recherche à partir d’une Razor page comprend le dossier *pages* .</span><span class="sxs-lookup"><span data-stu-id="eea4e-583">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="eea4e-584">Les mises en page, les modèles et les partiels que vous utilisez avec les contrôleurs MVC et les Razor vues conventionnelles *fonctionnent simplement*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-584">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="eea4e-585">Ajoutez un fichier *Pages/_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-585">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="eea4e-586">`@namespace` est expliqué plus loin dans le didacticiel.</span><span class="sxs-lookup"><span data-stu-id="eea4e-586">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="eea4e-587">La directive `@addTagHelper` permet de bénéficier des [Tag Helpers intégrés](xref:mvc/views/tag-helpers/builtin-th/Index) dans toutes les pages du dossier *Pages*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-587">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="eea4e-588">Quand la directive `@namespace` est utilisée explicitement sur une page :</span><span class="sxs-lookup"><span data-stu-id="eea4e-588">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="eea4e-589">La directive définit l’espace de noms pour la page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-589">The directive sets the namespace for the page.</span></span> <span data-ttu-id="eea4e-590">La directive `@model` n’a pas besoin d’inclure l’espace de noms.</span><span class="sxs-lookup"><span data-stu-id="eea4e-590">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="eea4e-591">Quand la directive `@namespace` est contenue dans *_ViewImports.cshtml*, l’espace de noms spécifié fournit le préfixe de l’espace de noms généré dans la Page qui importe la directive `@namespace`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-591">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="eea4e-592">Le reste de l’espace de noms généré (la partie suffixe) est le chemin relatif séparé par un point entre le dossier contenant *_ViewImports.cshtml* et le dossier contenant la page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-592">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="eea4e-593">Par exemple, la classe `PageModel`*Pages/Customers/Edit.cshtml.cs* définit explicitement l’espace de noms :</span><span class="sxs-lookup"><span data-stu-id="eea4e-593">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="eea4e-594">Le fichier *Pages/_ViewImports.cshtml* définit l’espace de noms suivant :</span><span class="sxs-lookup"><span data-stu-id="eea4e-594">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="eea4e-595">L’espace de noms généré pour la page *pages/Customers/Edit. cshtml* Razor est le même que la `PageModel` classe.</span><span class="sxs-lookup"><span data-stu-id="eea4e-595">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="eea4e-596">`@namespace`*fonctionne également avec les Razor vues conventionnelles.*</span><span class="sxs-lookup"><span data-stu-id="eea4e-596">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="eea4e-597">Le fichier de vue *Pages/Create.cshtml* d’origine :</span><span class="sxs-lookup"><span data-stu-id="eea4e-597">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="eea4e-598">Le fichier vue *Pages/Create.cshtml* mis à jour :</span><span class="sxs-lookup"><span data-stu-id="eea4e-598">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="eea4e-599">Le [ Razor projet de démarrage pages](#rpvs17) contient les *pages/_ValidationScriptsPartial. cshtml*, qui raccorde la validation côté client.</span><span class="sxs-lookup"><span data-stu-id="eea4e-599">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="eea4e-600">Pour plus d'informations sur les affichages partiels, consultez <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="eea4e-600">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="eea4e-601">Génération d’URL pour les pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-601">URL generation for Pages</span></span>

<span data-ttu-id="eea4e-602">La page `Create`, illustrée précédemment, utilise `RedirectToPage` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-602">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="eea4e-603">L’application a la structure de fichiers/dossiers suivante :</span><span class="sxs-lookup"><span data-stu-id="eea4e-603">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="eea4e-604">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="eea4e-604">*/Pages*</span></span>

  * <span data-ttu-id="eea4e-605">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-605">*Index.cshtml*</span></span>
  * <span data-ttu-id="eea4e-606">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="eea4e-606">*/Customers*</span></span>

    * <span data-ttu-id="eea4e-607">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-607">*Create.cshtml*</span></span>
    * <span data-ttu-id="eea4e-608">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-608">*Edit.cshtml*</span></span>
    * <span data-ttu-id="eea4e-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eea4e-609">*Index.cshtml*</span></span>

<span data-ttu-id="eea4e-610">Une fois l’opération réussie, les pages *Pages/Customers/Create.cshtml* et *Pages/Customers/Edit.cshtml* redirigent vers *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-610">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="eea4e-611">La chaîne `/Index` fait partie de l’URI pour accéder à la page précédente.</span><span class="sxs-lookup"><span data-stu-id="eea4e-611">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="eea4e-612">La chaîne `/Index` peut être utilisée pour générer l’URI de la page *Pages/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-612">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="eea4e-613">Exemple :</span><span class="sxs-lookup"><span data-stu-id="eea4e-613">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="eea4e-614">Le nom de la page est le chemin de la page à partir du dossier racine */Pages* avec un `/` devant (par exemple, `/Index`).</span><span class="sxs-lookup"><span data-stu-id="eea4e-614">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="eea4e-615">Les exemples de génération d’URL précédents offrent des options améliorées et des capacités fonctionnelles sur le codage en dur d’une URL.</span><span class="sxs-lookup"><span data-stu-id="eea4e-615">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="eea4e-616">La génération d’URL utilise le [routage](xref:mvc/controllers/routing) et peut générer et encoder des paramètres en fonction de la façon dont l’itinéraire est défini dans le chemin de destination.</span><span class="sxs-lookup"><span data-stu-id="eea4e-616">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="eea4e-617">La génération d’URL pour les pages prend en charge les noms relatifs.</span><span class="sxs-lookup"><span data-stu-id="eea4e-617">URL generation for pages supports relative names.</span></span> <span data-ttu-id="eea4e-618">Le tableau suivant montre la page Index sélectionnée avec différents paramètres `RedirectToPage` à partir de *Pages/Customers/Create.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="eea4e-618">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="eea4e-619">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="eea4e-619">RedirectToPage(x)</span></span>| <span data-ttu-id="eea4e-620">Page</span><span class="sxs-lookup"><span data-stu-id="eea4e-620">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="eea4e-621">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="eea4e-621">RedirectToPage("/Index")</span></span> | <span data-ttu-id="eea4e-622">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="eea4e-622">*Pages/Index*</span></span> |
| <span data-ttu-id="eea4e-623">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="eea4e-623">RedirectToPage("./Index");</span></span> | <span data-ttu-id="eea4e-624">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="eea4e-624">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="eea4e-625">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="eea4e-625">RedirectToPage("../Index")</span></span> | <span data-ttu-id="eea4e-626">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="eea4e-626">*Pages/Index*</span></span> |
| <span data-ttu-id="eea4e-627">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="eea4e-627">RedirectToPage("Index")</span></span>  | <span data-ttu-id="eea4e-628">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="eea4e-628">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="eea4e-629">`RedirectToPage("Index")`, `RedirectToPage("./Index")` et `RedirectToPage("../Index")`  sont des *noms relatifs*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-629">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="eea4e-630">Le paramètre `RedirectToPage` est *combiné* avec le chemin de la page active pour calculer le nom de la page de destination.</span><span class="sxs-lookup"><span data-stu-id="eea4e-630">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="eea4e-631">La liaison de nom relatif est utile lors de la création de sites avec une structure complexe.</span><span class="sxs-lookup"><span data-stu-id="eea4e-631">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="eea4e-632">Si vous utilisez des noms relatifs pour établir une liaison entre les pages d’un dossier, vous pouvez renommer ce dossier.</span><span class="sxs-lookup"><span data-stu-id="eea4e-632">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="eea4e-633">Tous les liens fonctionneront encore (car ils n’incluent pas le nom du dossier).</span><span class="sxs-lookup"><span data-stu-id="eea4e-633">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="eea4e-634">Pour rediriger vers une page située dans une autre [Zone](xref:mvc/controllers/areas), spécifiez la zone :</span><span class="sxs-lookup"><span data-stu-id="eea4e-634">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="eea4e-635">Pour plus d'informations, consultez <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="eea4e-635">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="eea4e-636">Attribut ViewData</span><span class="sxs-lookup"><span data-stu-id="eea4e-636">ViewData attribute</span></span>

<span data-ttu-id="eea4e-637">Les données peuvent être passées à une page avec [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span><span class="sxs-lookup"><span data-stu-id="eea4e-637">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="eea4e-638">Les propriétés sur les contrôleurs ou Razor les modèles de page avec l' `[ViewData]` attribut ont leurs valeurs stockées et chargées à partir du [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span><span class="sxs-lookup"><span data-stu-id="eea4e-638">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="eea4e-639">Dans l’exemple suivant, le `AboutModel` contient une `Title` propriété marquée avec `[ViewData]` .</span><span class="sxs-lookup"><span data-stu-id="eea4e-639">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="eea4e-640">La propriété `Title` a pour valeur le titre de la page À propos de :</span><span class="sxs-lookup"><span data-stu-id="eea4e-640">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="eea4e-641">Dans la page À propos de, accédez à la propriété `Title` en tant que propriété de modèle :</span><span class="sxs-lookup"><span data-stu-id="eea4e-641">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="eea4e-642">Dans la disposition, le titre est lu à partir du dictionnaire ViewData :</span><span class="sxs-lookup"><span data-stu-id="eea4e-642">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="eea4e-643">TempData</span><span class="sxs-lookup"><span data-stu-id="eea4e-643">TempData</span></span>

<span data-ttu-id="eea4e-644">ASP.NET Core expose la propriété [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) sur un [contrôleur](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span><span class="sxs-lookup"><span data-stu-id="eea4e-644">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="eea4e-645">Cette propriété stocke les données jusqu’à ce qu’elles soient lues.</span><span class="sxs-lookup"><span data-stu-id="eea4e-645">This property stores data until it's read.</span></span> <span data-ttu-id="eea4e-646">Vous pouvez utiliser les méthodes `Keep` et `Peek` pour examiner les données sans suppression.</span><span class="sxs-lookup"><span data-stu-id="eea4e-646">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="eea4e-647">`TempData` est utile pour la redirection, quand des données sont nécessaires pour plusieurs requêtes.</span><span class="sxs-lookup"><span data-stu-id="eea4e-647">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="eea4e-648">Le code suivant définit la valeur de `Message` à l’aide de `TempData` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-648">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="eea4e-649">Le balisage suivant dans le fichier *Pages/Customers/Index.cshtml* affiche la valeur de `Message` à l’aide de `TempData`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-649">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="eea4e-650">Le modèle de page *Pages/Customers/Index.cshtml.cs* applique l’attribut `[TempData]` à la propriété `Message`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-650">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="eea4e-651">Pour plus d’informations, consultez [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="eea4e-651">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="eea4e-652">Plusieurs gestionnaires par page</span><span class="sxs-lookup"><span data-stu-id="eea4e-652">Multiple handlers per page</span></span>

<span data-ttu-id="eea4e-653">La page suivante génère un balisage pour deux gestionnaires en utilisant le Tag Helper `asp-page-handler` :</span><span class="sxs-lookup"><span data-stu-id="eea4e-653">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="eea4e-654">Le formulaire dans l’exemple précédent contient deux boutons d’envoi, chacun utilisant `FormActionTagHelper` pour envoyer à une URL différente.</span><span class="sxs-lookup"><span data-stu-id="eea4e-654">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="eea4e-655">L’attribut `asp-page-handler` est un complément de `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-655">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="eea4e-656">`asp-page-handler` génère des URL qui envoient à chacune des méthodes de gestionnaire définies par une page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-656">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="eea4e-657">`asp-page` n’est pas spécifié, car l’exemple établit une liaison à la page active.</span><span class="sxs-lookup"><span data-stu-id="eea4e-657">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="eea4e-658">Le modèle de page :</span><span class="sxs-lookup"><span data-stu-id="eea4e-658">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="eea4e-659">Le code précédent utilise des *méthodes de gestionnaire nommées*.</span><span class="sxs-lookup"><span data-stu-id="eea4e-659">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="eea4e-660">Pour créer des méthodes de gestionnaire nommées, il faut prendre le texte dans le nom après `On<HTTP Verb>` et avant `Async` (le cas échéant).</span><span class="sxs-lookup"><span data-stu-id="eea4e-660">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="eea4e-661">Dans l’exemple précédent, les méthodes de page sont OnPost **JoinList** Async et OnPost **JoinListUC** Async.</span><span class="sxs-lookup"><span data-stu-id="eea4e-661">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="eea4e-662">Avec *OnPost* et *Async* supprimés, les noms des gestionnaires sont `JoinList` et `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-662">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="eea4e-663">Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-663">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="eea4e-664">Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-664">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="eea4e-665">Routes personnalisées</span><span class="sxs-lookup"><span data-stu-id="eea4e-665">Custom routes</span></span>

<span data-ttu-id="eea4e-666">Utilisez la directive `@page` pour :</span><span class="sxs-lookup"><span data-stu-id="eea4e-666">Use the `@page` directive to:</span></span>

* <span data-ttu-id="eea4e-667">Spécifier une route personnalisée vers une page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-667">Specify a custom route to a page.</span></span> <span data-ttu-id="eea4e-668">Par exemple, la route vers la page À propos peut être définie sur `/Some/Other/Path` avec `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-668">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="eea4e-669">Ajouter des segments à la route par défaut d’une page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-669">Append segments to a page's default route.</span></span> <span data-ttu-id="eea4e-670">Par exemple, un segment « item » peut être ajouté à la route par défaut d’une page avec `@page "item"`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-670">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="eea4e-671">Ajouter des paramètres à la route par défaut d’une page.</span><span class="sxs-lookup"><span data-stu-id="eea4e-671">Append parameters to a page's default route.</span></span> <span data-ttu-id="eea4e-672">Par exemple, un paramètre d’ID, `id`, peut être nécessaire pour une page avec `@page "{id}"`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-672">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="eea4e-673">Un chemin relatif racine désigné par un tilde (`~`) au début du chemin est pris en charge.</span><span class="sxs-lookup"><span data-stu-id="eea4e-673">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="eea4e-674">Par exemple, `@page "~/Some/Other/Path"` est identique à `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-674">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="eea4e-675">Si vous n’aimez pas la chaîne `?handler=JoinList` de requête dans l’URL, modifiez l’itinéraire pour placer le nom du gestionnaire dans la partie chemin d’accès de l’URL.</span><span class="sxs-lookup"><span data-stu-id="eea4e-675">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="eea4e-676">L’itinéraire peut être personnalisé en ajoutant un modèle de routage entre guillemets doubles après la `@page` directive.</span><span class="sxs-lookup"><span data-stu-id="eea4e-676">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="eea4e-677">Avec le code précédent, le chemin d’URL qui envoie à `OnPostJoinListAsync` est `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-677">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="eea4e-678">Le chemin d’URL qui envoie à `OnPostJoinListUCAsync` est `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="eea4e-678">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="eea4e-679">Le `?` suivant `handler` signifie que le paramètre d’itinéraire est facultatif.</span><span class="sxs-lookup"><span data-stu-id="eea4e-679">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="eea4e-680">Configuration et paramètres</span><span class="sxs-lookup"><span data-stu-id="eea4e-680">Configuration and settings</span></span>

<span data-ttu-id="eea4e-681">Pour configurer les options avancées, utilisez la méthode d’extension `AddRazorPagesOptions` sur le générateur MVC :</span><span class="sxs-lookup"><span data-stu-id="eea4e-681">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="eea4e-682">Actuellement, vous pouvez utiliser `RazorPagesOptions` pour définir le répertoire racine pour les pages, ou ajouter des conventions de modèle d’application pour les pages.</span><span class="sxs-lookup"><span data-stu-id="eea4e-682">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="eea4e-683">Nous permettrons à l’avenir une plus grande extensibilité en ce sens.</span><span class="sxs-lookup"><span data-stu-id="eea4e-683">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="eea4e-684">Pour précompiler des vues, consultez [ Razor afficher la compilation](xref:mvc/views/view-compilation) .</span><span class="sxs-lookup"><span data-stu-id="eea4e-684">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="eea4e-685">[Téléchargez ou affichez des exemples de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="eea4e-685">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="eea4e-686">Consultez [prise en main des Razor pages](xref:tutorials/razor-pages/razor-pages-start), qui s’appuie sur cette introduction.</span><span class="sxs-lookup"><span data-stu-id="eea4e-686">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="eea4e-687">Spécifier que Razor les pages se trouvent à la racine du contenu</span><span class="sxs-lookup"><span data-stu-id="eea4e-687">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="eea4e-688">Par défaut, Razor les pages sont enracinées dans le répertoire */pages*</span><span class="sxs-lookup"><span data-stu-id="eea4e-688">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="eea4e-689">Ajoutez [avec Razor PagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) à [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) pour spécifier que vos Razor pages se trouvent à la [racine du contenu](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) de l’application :</span><span class="sxs-lookup"><span data-stu-id="eea4e-689">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="eea4e-690">Spécifier que Razor les pages se trouvent dans un répertoire racine personnalisé</span><span class="sxs-lookup"><span data-stu-id="eea4e-690">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="eea4e-691">Ajoutez [avec Razor PagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) à [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) pour spécifier que vos Razor pages se trouvent dans un répertoire racine personnalisé dans l’application (fournissez un chemin d’accès relatif) :</span><span class="sxs-lookup"><span data-stu-id="eea4e-691">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="eea4e-692">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="eea4e-692">Additional resources</span></span>

* [<span data-ttu-id="eea4e-693">Autoriser l’attribut et les Razor pages</span><span class="sxs-lookup"><span data-stu-id="eea4e-693">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* [<span data-ttu-id="eea4e-694">Razor Référence de syntaxe pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eea4e-694">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
