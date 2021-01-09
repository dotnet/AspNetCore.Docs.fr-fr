---
title: Composants de vue dans ASP.NET Core
author: rick-anderson
description: Découvrez comment les composants de vue sont utilisés dans ASP.NET Core et comment les ajouter à des applications.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
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
uid: mvc/views/view-components
ms.openlocfilehash: 61fcc07aeb30db15014fb716194328d366f27859
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058335"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="9aa91-103">Composants de vue dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9aa91-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="9aa91-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9aa91-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9aa91-105">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9aa91-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="9aa91-106">Composants de vue</span><span class="sxs-lookup"><span data-stu-id="9aa91-106">View components</span></span>

<span data-ttu-id="9aa91-107">Les composants de vue sont similaires aux vues partielles, mais ils sont beaucoup plus puissants.</span><span class="sxs-lookup"><span data-stu-id="9aa91-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="9aa91-108">Les composants de vue n’utilisent pas la liaison de données ; ils dépendent uniquement des données fournies en réponse à l’appel d’un composant de vue.</span><span class="sxs-lookup"><span data-stu-id="9aa91-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="9aa91-109">Cet article a été écrit à l’aide de contrôleurs et de vues, mais les composants de vue fonctionnent également avec les Razor pages.</span><span class="sxs-lookup"><span data-stu-id="9aa91-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="9aa91-110">Un composant de vue a les caractéristiques suivantes :</span><span class="sxs-lookup"><span data-stu-id="9aa91-110">A view component:</span></span>

* <span data-ttu-id="9aa91-111">Il effectue le rendu d’un bloc de code au lieu d’une réponse entière.</span><span class="sxs-lookup"><span data-stu-id="9aa91-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="9aa91-112">Il garantit la même « séparation des préoccupations » et offre les mêmes avantages de testabilité qu’entre un contrôleur et une vue.</span><span class="sxs-lookup"><span data-stu-id="9aa91-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="9aa91-113">Il peut avoir des paramètres et une logique métier.</span><span class="sxs-lookup"><span data-stu-id="9aa91-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="9aa91-114">Il est généralement appelé à partir d’une page de disposition.</span><span class="sxs-lookup"><span data-stu-id="9aa91-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="9aa91-115">Les composants de vue sont conçus pour être utilisés là où vous avez une logique de rendu réutilisable qui est trop complexe pour une vue partielle. Ils ciblent les vues suivantes, par exemple :</span><span class="sxs-lookup"><span data-stu-id="9aa91-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="9aa91-116">Menus de navigation dynamiques</span><span class="sxs-lookup"><span data-stu-id="9aa91-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="9aa91-117">Nuage de mots clés (pour l’interrogation de la base de données)</span><span class="sxs-lookup"><span data-stu-id="9aa91-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="9aa91-118">Panneau de connexion</span><span class="sxs-lookup"><span data-stu-id="9aa91-118">Login panel</span></span>
* <span data-ttu-id="9aa91-119">Panier d’achat</span><span class="sxs-lookup"><span data-stu-id="9aa91-119">Shopping cart</span></span>
* <span data-ttu-id="9aa91-120">Articles récemment publiés</span><span class="sxs-lookup"><span data-stu-id="9aa91-120">Recently published articles</span></span>
* <span data-ttu-id="9aa91-121">Contenu de la barre latérale dans un blog standard</span><span class="sxs-lookup"><span data-stu-id="9aa91-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="9aa91-122">Panneau de connexion inclus dans chaque page pour afficher les liens de connexion ou déconnexion, en fonction de l’état de connexion de l’utilisateur</span><span class="sxs-lookup"><span data-stu-id="9aa91-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="9aa91-123">Un composant de vue a deux éléments : sa classe (généralement dérivée de [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) et le résultat qu’il retourne (en général, une vue).</span><span class="sxs-lookup"><span data-stu-id="9aa91-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="9aa91-124">Comme les contrôleurs, un composant de vue peut être un OCT, mais la plupart des développeurs préfèrent utiliser les méthodes et propriétés dérivées de `ViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="9aa91-125">Si vous envisagez que les composants de vue répondent aux spécifications d’une application, envisagez plutôt d’utiliser des Razor composants.</span><span class="sxs-lookup"><span data-stu-id="9aa91-125">When considering if view components meet an app's specifications, consider using Razor components instead.</span></span> <span data-ttu-id="9aa91-126">Razor les composants combinent également le balisage avec du code C# pour produire des unités d’interface utilisateur réutilisables.</span><span class="sxs-lookup"><span data-stu-id="9aa91-126">Razor components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="9aa91-127">Razor les composants sont conçus pour la productivité des développeurs lorsqu’ils fournissent la logique de l’interface utilisateur côté client et la composition.</span><span class="sxs-lookup"><span data-stu-id="9aa91-127">Razor components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="9aa91-128">Pour plus d'informations, consultez <xref:blazor/components/index>.</span><span class="sxs-lookup"><span data-stu-id="9aa91-128">For more information, see <xref:blazor/components/index>.</span></span> <span data-ttu-id="9aa91-129">Pour plus d’informations sur la façon d’incorporer des Razor composants dans une application MVC ou Razor pages, consultez <xref:blazor/components/prerendering-and-integration?pivots=server> .</span><span class="sxs-lookup"><span data-stu-id="9aa91-129">For information on how to incorporate Razor components into an MVC or Razor Pages app, see <xref:blazor/components/prerendering-and-integration?pivots=server>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="9aa91-130">Création d’un composant de vue</span><span class="sxs-lookup"><span data-stu-id="9aa91-130">Creating a view component</span></span>

<span data-ttu-id="9aa91-131">Cette section présente les exigences générales relatives à la création d’un composant de vue.</span><span class="sxs-lookup"><span data-stu-id="9aa91-131">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="9aa91-132">Plus loin dans cet article, nous décrirons chaque étape en détail et nous créerons un composant de vue.</span><span class="sxs-lookup"><span data-stu-id="9aa91-132">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="9aa91-133">Classe de composant de vue</span><span class="sxs-lookup"><span data-stu-id="9aa91-133">The view component class</span></span>

<span data-ttu-id="9aa91-134">Vous pouvez créer une classe de composant de vue à l’aide d’une des méthodes suivantes :</span><span class="sxs-lookup"><span data-stu-id="9aa91-134">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="9aa91-135">En la dérivant de *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="9aa91-135">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="9aa91-136">En décorant une classe avec l’attribut `[ViewComponent]` ou en dérivant la classe d’une classe définie avec l’attribut `[ViewComponent]`</span><span class="sxs-lookup"><span data-stu-id="9aa91-136">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="9aa91-137">En créant une classe dont le nom se termine par le suffixe *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="9aa91-137">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="9aa91-138">Comme les contrôleurs, les composants de vue doivent être des classes publiques, non imbriquées et non abstraites.</span><span class="sxs-lookup"><span data-stu-id="9aa91-138">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="9aa91-139">Le nom du composant de vue correspond au nom de la classe sans le suffixe ViewComponent.</span><span class="sxs-lookup"><span data-stu-id="9aa91-139">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="9aa91-140">Il peut également être spécifié explicitement à l’aide de la propriété `ViewComponentAttribute.Name`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-140">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="9aa91-141">Une classe de composant de vue :</span><span class="sxs-lookup"><span data-stu-id="9aa91-141">A view component class:</span></span>

* <span data-ttu-id="9aa91-142">Prend entièrement en charge [l’injection de dépendances](../../fundamentals/dependency-injection.md) dans le constructeur</span><span class="sxs-lookup"><span data-stu-id="9aa91-142">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="9aa91-143">N’intervient pas dans le cycle de vie du contrôleur, ce qui signifie que vous ne pouvez pas utiliser de [filtres](../controllers/filters.md) dans un composant de vue</span><span class="sxs-lookup"><span data-stu-id="9aa91-143">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="9aa91-144">Méthodes d’un composant de vue</span><span class="sxs-lookup"><span data-stu-id="9aa91-144">View component methods</span></span>

<span data-ttu-id="9aa91-145">Un composant de vue définit sa logique dans une méthode `InvokeAsync` qui retourne un `Task<IViewComponentResult>`, ou dans une méthode `Invoke` synchrone qui retourne un `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-145">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="9aa91-146">Les paramètres sont fournis directement en réponse à l’appel du composant de vue ; ils ne proviennent pas de la liaison de données.</span><span class="sxs-lookup"><span data-stu-id="9aa91-146">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="9aa91-147">Un composant de vue ne traite jamais une requête directement.</span><span class="sxs-lookup"><span data-stu-id="9aa91-147">A view component never directly handles a request.</span></span> <span data-ttu-id="9aa91-148">En règle générale, un composant de vue initialise un modèle et le passe à une vue en appelant la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-148">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="9aa91-149">En résumé, les méthodes d’un composant de vue :</span><span class="sxs-lookup"><span data-stu-id="9aa91-149">In summary, view component methods:</span></span>

* <span data-ttu-id="9aa91-150">Définissent une `InvokeAsync` méthode qui retourne un `Task<IViewComponentResult>` ou une méthode `Invoke` synchrone qui retourne un `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-150">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="9aa91-151">Initialise généralement un modèle et le passe à une vue en appelant la `ViewComponent` `View` méthode.</span><span class="sxs-lookup"><span data-stu-id="9aa91-151">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="9aa91-152">Les paramètres proviennent de la méthode appelante, et non pas de HTTP.</span><span class="sxs-lookup"><span data-stu-id="9aa91-152">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="9aa91-153">Il n’y a pas de liaison de modèle.</span><span class="sxs-lookup"><span data-stu-id="9aa91-153">There's no model binding.</span></span>
* <span data-ttu-id="9aa91-154">Ne sont pas accessibles directement comme point de terminaison HTTP.</span><span class="sxs-lookup"><span data-stu-id="9aa91-154">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="9aa91-155">Elles sont appelées depuis votre code (généralement dans une vue).</span><span class="sxs-lookup"><span data-stu-id="9aa91-155">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="9aa91-156">Un composant de vue ne traite jamais une requête.</span><span class="sxs-lookup"><span data-stu-id="9aa91-156">A view component never handles a request.</span></span>
* <span data-ttu-id="9aa91-157">Sont surchargées sur la signature, plutôt que sur des détails de la requête HTTP en cours.</span><span class="sxs-lookup"><span data-stu-id="9aa91-157">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="9aa91-158">Chemin de recherche de la vue</span><span class="sxs-lookup"><span data-stu-id="9aa91-158">View search path</span></span>

<span data-ttu-id="9aa91-159">Le Runtime recherche la vue dans les chemins suivants :</span><span class="sxs-lookup"><span data-stu-id="9aa91-159">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="9aa91-160">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="9aa91-160">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="9aa91-161">/Views/Shared/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="9aa91-161">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="9aa91-162">/Pages/Shared/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="9aa91-162">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="9aa91-163">Le chemin de recherche s’applique aux projets qui utilisent des contrôleurs, des vues et des Razor pages.</span><span class="sxs-lookup"><span data-stu-id="9aa91-163">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="9aa91-164">Le nom de la vue par défaut pour un composant de vue est *Default*. Votre fichier de vue est donc normalement appelé *Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-164">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="9aa91-165">Vous pouvez spécifier un nom de vue différent quand vous créez le résultat du composant de vue ou quand vous appelez la méthode `View`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-165">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="9aa91-166">Nous vous recommandons de nommer le fichier de vue *Default.cshtml* et d’utiliser le chemin *Views/Shared/Components/{View Component Name}/{View Name}*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-166">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="9aa91-167">Le composant de vue `PriorityList` montré dans cet exemple utilise le chemin *Views/Shared/Components/PriorityList/Default.cshtml* pour la vue.</span><span class="sxs-lookup"><span data-stu-id="9aa91-167">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="9aa91-168">Personnaliser le chemin de recherche d’affichage</span><span class="sxs-lookup"><span data-stu-id="9aa91-168">Customize the view search path</span></span>

<span data-ttu-id="9aa91-169">Pour personnaliser le chemin de recherche d’affichage, la Razor collection de la modification <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> .</span><span class="sxs-lookup"><span data-stu-id="9aa91-169">To customize the view search path, modify Razor's <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="9aa91-170">Par exemple, pour rechercher des affichages dans le chemin d’accès « /Components/{View Component Name}/{View Name} », ajoutez un nouvel élément à la collection :</span><span class="sxs-lookup"><span data-stu-id="9aa91-170">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-csharp[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="9aa91-171">Dans le code précédent, l’espace réservé « {0} » représente le chemin d’accès « Components/{View Component Name}/{view Name} ».</span><span class="sxs-lookup"><span data-stu-id="9aa91-171">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="9aa91-172">Appel d’un composant de vue</span><span class="sxs-lookup"><span data-stu-id="9aa91-172">Invoking a view component</span></span>

<span data-ttu-id="9aa91-173">Pour utiliser le composant de vue, appelez-le dans une vue, comme ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="9aa91-173">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="9aa91-174">Les paramètres sont passés à la méthode `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-174">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="9aa91-175">Le composant de vue `PriorityList` décrit dans cet article est appelé du fichier de vue *Views/ToDo/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-175">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="9aa91-176">Dans le code suivant, la méthode `InvokeAsync` est appelée avec deux paramètres :</span><span class="sxs-lookup"><span data-stu-id="9aa91-176">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="9aa91-177">Appel d’un composant de vue en tant que Tag Helper</span><span class="sxs-lookup"><span data-stu-id="9aa91-177">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="9aa91-178">Dans ASP.NET Core 1.1 et les versions ultérieures, vous pouvez appeler un composant de vue en tant que [Tag Helper](xref:mvc/views/tag-helpers/intro) :</span><span class="sxs-lookup"><span data-stu-id="9aa91-178">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="9aa91-179">Les paramètres de méthode et de classe de casse Pascal pour les Tag Helpers sont convertis en [casse kebab](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101) (mots séparés par des tirets).</span><span class="sxs-lookup"><span data-stu-id="9aa91-179">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="9aa91-180">Le Tag Helper utilisé pour appeler un composant de vue contient l’élément `<vc></vc>`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-180">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="9aa91-181">Le composant de vue est spécifié de la façon suivante :</span><span class="sxs-lookup"><span data-stu-id="9aa91-181">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="9aa91-182">Pour utiliser un composant de vue en tant que Tag Helper, inscrivez l’assembly contenant le composant de vue à l’aide de la directive `@addTagHelper`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-182">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="9aa91-183">Si votre composant de vue se trouve dans un assembly appelé `MyWebApp`, ajoutez la directive suivante dans le fichier *_ViewImports.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9aa91-183">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="9aa91-184">Vous pouvez inscrire un composant de vue en tant que Tag Helper dans n’importe quel fichier qui référence ce composant.</span><span class="sxs-lookup"><span data-stu-id="9aa91-184">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="9aa91-185">Pour plus d’informations sur l’inscription des Tag Helpers, consultez [Gestion de l’étendue des Tag Helpers](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope).</span><span class="sxs-lookup"><span data-stu-id="9aa91-185">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="9aa91-186">Méthode `InvokeAsync` utilisée dans ce didacticiel :</span><span class="sxs-lookup"><span data-stu-id="9aa91-186">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="9aa91-187">Dans le balisage Tag Helper :</span><span class="sxs-lookup"><span data-stu-id="9aa91-187">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="9aa91-188">Dans l’exemple ci-dessus, le composant de vue `PriorityList` devient `priority-list`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-188">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="9aa91-189">Les paramètres sont passés au composant de vue sous forme d’attributs dans la casse kebab.</span><span class="sxs-lookup"><span data-stu-id="9aa91-189">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="9aa91-190">Appel d’un composant de vue directement à partir d’un contrôleur</span><span class="sxs-lookup"><span data-stu-id="9aa91-190">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="9aa91-191">Les composants de vue sont généralement appelés depuis une vue, mais ils peuvent aussi être appelés directement d’une méthode de contrôleur.</span><span class="sxs-lookup"><span data-stu-id="9aa91-191">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="9aa91-192">Les composants de vue ne définissent pas de points de terminaison comme le font les contrôleurs, mais vous pouvez facilement implémenter une action de contrôleur qui retourne le contenu d’un `ViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-192">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="9aa91-193">Dans cet exemple, le composant de vue est appelé directement du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="9aa91-193">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="9aa91-194">Procédure pas à pas : Création d’un composant de vue simple</span><span class="sxs-lookup"><span data-stu-id="9aa91-194">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="9aa91-195">[Téléchargez](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), générez et testez le code de démarrage.</span><span class="sxs-lookup"><span data-stu-id="9aa91-195">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="9aa91-196">Il s’agit d’un projet simple avec un contrôleur `ToDo` qui affiche une liste de tâches *ToDo*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-196">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![Liste des tâches Todo](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="9aa91-198">Ajouter une classe ViewComponent</span><span class="sxs-lookup"><span data-stu-id="9aa91-198">Add a ViewComponent class</span></span>

<span data-ttu-id="9aa91-199">Créez un dossier *ViewComponents* et ajoutez la classe `PriorityListViewComponent` suivante :</span><span class="sxs-lookup"><span data-stu-id="9aa91-199">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="9aa91-200">Remarques sur le code :</span><span class="sxs-lookup"><span data-stu-id="9aa91-200">Notes on the code:</span></span>

* <span data-ttu-id="9aa91-201">Les classes ViewComponent peuvent être ajoutées à **n’importe** quel dossier dans le projet.</span><span class="sxs-lookup"><span data-stu-id="9aa91-201">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="9aa91-202">Étant donné que le nom de classe PriorityList **ViewComponent** se termine par le suffixe **ViewComponent**, le Runtime utilise la chaîne « PriorityList » pour référencer le composant de la classe à partir d’une vue.</span><span class="sxs-lookup"><span data-stu-id="9aa91-202">Because the class name PriorityList **ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="9aa91-203">J’expliquerai ce point plus en détail plus tard.</span><span class="sxs-lookup"><span data-stu-id="9aa91-203">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="9aa91-204">L’attribut `[ViewComponent]` peut changer le nom utilisé pour faire référence à un composant de vue.</span><span class="sxs-lookup"><span data-stu-id="9aa91-204">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="9aa91-205">Par exemple, nous pourrions avoir nommé la classe `XYZ` et appliqué l’attribut `ViewComponent` :</span><span class="sxs-lookup"><span data-stu-id="9aa91-205">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="9aa91-206">L’attribut `[ViewComponent]` ci-dessus indique au sélecteur du composant de vue d’utiliser le nom `PriorityList` pour rechercher les vues associées au composant et d’utiliser la chaîne « PriorityList » pour faire référence au composant de la classe à partir d’une vue.</span><span class="sxs-lookup"><span data-stu-id="9aa91-206">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="9aa91-207">J’expliquerai ce point plus en détail plus tard.</span><span class="sxs-lookup"><span data-stu-id="9aa91-207">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="9aa91-208">Le composant utilise [l’injection de dépendances](../../fundamentals/dependency-injection.md) pour rendre le contexte de données disponible.</span><span class="sxs-lookup"><span data-stu-id="9aa91-208">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="9aa91-209">`InvokeAsync` expose une méthode qui peut être appelée à partir d’une vue et qui peut prendre un nombre arbitraire d’arguments.</span><span class="sxs-lookup"><span data-stu-id="9aa91-209">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="9aa91-210">La méthode `InvokeAsync` retourne l’ensemble des tâches `ToDo` qui correspondent aux paramètres `isDone` et `maxPriority` spécifiés.</span><span class="sxs-lookup"><span data-stu-id="9aa91-210">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-no-locrazor-view"></a><span data-ttu-id="9aa91-211">Créer la vue de composant de vue Razor</span><span class="sxs-lookup"><span data-stu-id="9aa91-211">Create the view component Razor view</span></span>

* <span data-ttu-id="9aa91-212">Créez le dossier *Views/Shared/Components*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-212">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="9aa91-213">Ce dossier **doit** être nommé *Components*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-213">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="9aa91-214">Créez le dossier *Views/Shared/Components/PriorityList*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-214">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="9aa91-215">Ce nom de dossier doit correspondre au nom de la classe du composant de vue, ou au nom de la classe sans le suffixe (dans le cas où vous avez suivi la convention et utilisé le suffixe *ViewComponent* dans le nom de la classe).</span><span class="sxs-lookup"><span data-stu-id="9aa91-215">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="9aa91-216">Si vous avez utilisé l’attribut `ViewComponent`, le nom de la classe doit correspondre à la désignation de l’attribut.</span><span class="sxs-lookup"><span data-stu-id="9aa91-216">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="9aa91-217">Créer une vue *Views/Shared/Components/PriorityList/default. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="9aa91-217">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="9aa91-218">La Razor vue prend la liste `TodoItem` et les affiche.</span><span class="sxs-lookup"><span data-stu-id="9aa91-218">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="9aa91-219">Si la méthode `InvokeAsync` du composant de vue ne passe pas le nom de la vue (comme dans notre exemple), le nom de vue *Default* est utilisé par convention.</span><span class="sxs-lookup"><span data-stu-id="9aa91-219">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="9aa91-220">Je vous montrerai comment passer le nom de la vue plus tard dans ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="9aa91-220">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="9aa91-221">Pour substituer le style par défaut d’un contrôleur spécifique, ajoutez une vue dans le dossier des vues du contrôleur (par exemple, *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-221">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="9aa91-222">Si le composant de vue est spécifique au contrôleur, vous pouvez l’ajouter au dossier du contrôleur (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="9aa91-222">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="9aa91-223">Ajoutez une balise `div` contenant un appel au composant de liste des priorités à la fin du fichier *Views/ToDo/index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9aa91-223">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="9aa91-224">Le balisage `@await Component.InvokeAsync` montre la syntaxe utilisée pour appeler un composant de vue.</span><span class="sxs-lookup"><span data-stu-id="9aa91-224">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="9aa91-225">Le premier argument est le nom du composant à appeler.</span><span class="sxs-lookup"><span data-stu-id="9aa91-225">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="9aa91-226">Les paramètres suivants sont passés au composant.</span><span class="sxs-lookup"><span data-stu-id="9aa91-226">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="9aa91-227">`InvokeAsync` peut prendre un nombre arbitraire d’arguments.</span><span class="sxs-lookup"><span data-stu-id="9aa91-227">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="9aa91-228">Tester l'application.</span><span class="sxs-lookup"><span data-stu-id="9aa91-228">Test the app.</span></span> <span data-ttu-id="9aa91-229">L’image suivante montre la liste des tâches ToDo et les tâches prioritaires :</span><span class="sxs-lookup"><span data-stu-id="9aa91-229">The following image shows the ToDo list and the priority items:</span></span>

![liste des tâches ToDo et tâches prioritaires](view-components/_static/pi.png)

<span data-ttu-id="9aa91-231">Vous pouvez également appeler le composant de vue directement à partir du contrôleur :</span><span class="sxs-lookup"><span data-stu-id="9aa91-231">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![tâches prioritaires retournées par IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="9aa91-233">Spécification d’un nom de vue</span><span class="sxs-lookup"><span data-stu-id="9aa91-233">Specifying a view name</span></span>

<span data-ttu-id="9aa91-234">Un composant de vue complexe peut avoir besoin de spécifier une autre vue que la vue par défaut dans certaines conditions.</span><span class="sxs-lookup"><span data-stu-id="9aa91-234">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="9aa91-235">Le code suivant montre comment spécifier la vue « PVC » à l’aide de la méthode `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-235">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="9aa91-236">Mettez à jour la méthode `InvokeAsync` dans la classe `PriorityListViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="9aa91-236">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="9aa91-237">Copiez le fichier *Views/Shared/Components/PriorityList/Default.cshtml* dans une vue nommée *Views/Shared/Components/PriorityList/PVC.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-237">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="9aa91-238">Ajoutez un titre pour indiquer que la vue PVC est utilisée.</span><span class="sxs-lookup"><span data-stu-id="9aa91-238">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="9aa91-239">Mettez à jour *Views/ToDo/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="9aa91-239">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="9aa91-240">Exécutez l’application et vérifiez la vue PVC.</span><span class="sxs-lookup"><span data-stu-id="9aa91-240">Run the app and verify PVC view.</span></span>

![Composant de vue des priorités](view-components/_static/pvc.png)

<span data-ttu-id="9aa91-242">Si la vue PVC ne s’affiche pas, vérifiez que vous appelez le composant de vue avec une priorité supérieure ou égale à 4.</span><span class="sxs-lookup"><span data-stu-id="9aa91-242">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="9aa91-243">Vérifier le chemin de la vue</span><span class="sxs-lookup"><span data-stu-id="9aa91-243">Examine the view path</span></span>

* <span data-ttu-id="9aa91-244">Changez le paramètre de priorité à une priorité inférieure ou égale à 3 pour empêcher l’affichage de la vue des priorités.</span><span class="sxs-lookup"><span data-stu-id="9aa91-244">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="9aa91-245">Renommez temporairement *Views/ToDo/Components/PriorityList/Default.cshtml* en *1Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-245">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="9aa91-246">Testez l’application. Vous obtenez l’erreur suivante :</span><span class="sxs-lookup"><span data-stu-id="9aa91-246">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="9aa91-247">Copiez *Views/ToDo/Components/PriorityList/1Default.cshtml* vers *Views/Shared/Components/PriorityList/Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-247">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="9aa91-248">Ajoutez du balisage à la vue ToDo *Shared* du composant de vue pour indiquer que la vue provient du dossier *Shared*.</span><span class="sxs-lookup"><span data-stu-id="9aa91-248">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="9aa91-249">Testez la vue de composant **Shared**.</span><span class="sxs-lookup"><span data-stu-id="9aa91-249">Test the **Shared** component view.</span></span>

![Affichage de tâches ToDo avec la vue de composant Shared](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="9aa91-251">Éviter les chaînes codées en dur</span><span class="sxs-lookup"><span data-stu-id="9aa91-251">Avoiding hard-coded strings</span></span>

<span data-ttu-id="9aa91-252">Pour éviter d’éventuels problèmes au moment de la compilation, vous pouvez remplacer le nom du composant de vue codé en dur par le nom de la classe.</span><span class="sxs-lookup"><span data-stu-id="9aa91-252">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="9aa91-253">Créez le composant de vue sans le suffixe « ViewComponent » :</span><span class="sxs-lookup"><span data-stu-id="9aa91-253">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="9aa91-254">Ajoutez une `using` instruction à votre Razor fichier de vue et utilisez l' `nameof` opérateur :</span><span class="sxs-lookup"><span data-stu-id="9aa91-254">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="9aa91-255">Effectuer un travail synchrone</span><span class="sxs-lookup"><span data-stu-id="9aa91-255">Perform synchronous work</span></span>

<span data-ttu-id="9aa91-256">Le framework gère l’appel d’une méthode `Invoke` synchrone si vous n’avez pas besoin d’effectuer un travail asynchrone.</span><span class="sxs-lookup"><span data-stu-id="9aa91-256">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="9aa91-257">La méthode suivante crée un composant de vue `Invoke` synchrone :</span><span class="sxs-lookup"><span data-stu-id="9aa91-257">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="9aa91-258">Le fichier du composant de vue Razor répertorie les chaînes passées à la `Invoke` méthode (*views/domotique/Components/PriorityList/default. cshtml*) :</span><span class="sxs-lookup"><span data-stu-id="9aa91-258">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="9aa91-259">Le composant de vue est appelé dans un Razor fichier (par exemple, *views/orig/index. cshtml*) à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="9aa91-259">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="9aa91-260">Tag Helper</span><span class="sxs-lookup"><span data-stu-id="9aa91-260">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="9aa91-261">Pour utiliser l’approche <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>, appelez `Component.InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="9aa91-261">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="9aa91-262">Le composant de vue est appelé dans un Razor fichier (par exemple, *views/orig/index. cshtml*) avec <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> .</span><span class="sxs-lookup"><span data-stu-id="9aa91-262">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="9aa91-263">Appelez `Component.InvokeAsync` :</span><span class="sxs-lookup"><span data-stu-id="9aa91-263">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="9aa91-264">Pour utiliser le Tag Helper, inscrivez l’assembly contenant le composant de vue à l’aide de la directive `@addTagHelper` (le composant de vue se trouve dans un assembly appelé `MyWebApp`) :</span><span class="sxs-lookup"><span data-stu-id="9aa91-264">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="9aa91-265">Utilisez le tag Helper du composant View dans le Razor fichier de balisage :</span><span class="sxs-lookup"><span data-stu-id="9aa91-265">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="9aa91-266">La signature de méthode de `PriorityList.Invoke` est synchrone, mais elle Razor recherche et appelle la méthode avec `Component.InvokeAsync` dans le fichier de balisage.</span><span class="sxs-lookup"><span data-stu-id="9aa91-266">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="9aa91-267">Tous les paramètres du composant de vue sont requis.</span><span class="sxs-lookup"><span data-stu-id="9aa91-267">All view component parameters are required</span></span>

<span data-ttu-id="9aa91-268">Chaque paramètre d’un composant de vue est un attribut requis.</span><span class="sxs-lookup"><span data-stu-id="9aa91-268">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="9aa91-269">Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/5011).</span><span class="sxs-lookup"><span data-stu-id="9aa91-269">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="9aa91-270">Si un paramètre est omis :</span><span class="sxs-lookup"><span data-stu-id="9aa91-270">If any  parameter is omitted:</span></span>

* <span data-ttu-id="9aa91-271">La signature de la méthode `InvokeAsync` ne correspond pas, et la méthode n’est pas exécutée.</span><span class="sxs-lookup"><span data-stu-id="9aa91-271">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="9aa91-272">ViewComponent n’affiche aucun balisage.</span><span class="sxs-lookup"><span data-stu-id="9aa91-272">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="9aa91-273">Aucune erreur n’est levée.</span><span class="sxs-lookup"><span data-stu-id="9aa91-273">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9aa91-274">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="9aa91-274">Additional resources</span></span>

* [<span data-ttu-id="9aa91-275">Injection de dépendances dans les vues</span><span class="sxs-lookup"><span data-stu-id="9aa91-275">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
