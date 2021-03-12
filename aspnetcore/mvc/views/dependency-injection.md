---
title: Injection de dépendances dans les vues dans ASP.NET Core
author: ardalis
description: Découvrez comment ASP.NET Core prend en charge l’injection de dépendances dans les vues MVC.
ms.author: riande
ms.date: 10/14/2016
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
uid: mvc/views/dependency-injection
ms.openlocfilehash: 9d4920a7b572163b048bfb06029d913cee3f9e5a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587251"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="5af15-103">Injection de dépendances dans les vues dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5af15-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="5af15-104">Par [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5af15-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5af15-105">ASP.NET Core prend en charge l’[injection de dépendances](xref:fundamentals/dependency-injection) dans les vues.</span><span class="sxs-lookup"><span data-stu-id="5af15-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="5af15-106">Cette fonctionnalité peut être utile pour les services spécifiques à une vue, notamment la localisation ou les données requises uniquement pour remplir les éléments de la vue.</span><span class="sxs-lookup"><span data-stu-id="5af15-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="5af15-107">Vous devez essayer de respecter le principe de [séparation des préoccupations](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) entre les contrôleurs et les vues.</span><span class="sxs-lookup"><span data-stu-id="5af15-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="5af15-108">La plupart des données affichées dans vos vues doivent être passées par le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="5af15-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="5af15-109">[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/dependency-injection/sample) ([procédure de téléchargement](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5af15-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="5af15-110">Injection de configuration</span><span class="sxs-lookup"><span data-stu-id="5af15-110">Configuration injection</span></span>

<span data-ttu-id="5af15-111">*appsettings.json* les valeurs peuvent être injectées directement dans une vue.</span><span class="sxs-lookup"><span data-stu-id="5af15-111">*appsettings.json* values can be injected directly into a view.</span></span>

<span data-ttu-id="5af15-112">Exemple de *appsettings.json* fichier :</span><span class="sxs-lookup"><span data-stu-id="5af15-112">Example of an *appsettings.json* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="5af15-113">Syntaxe de la directive `@inject` : `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="5af15-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="5af15-114">Exemple avec `@inject` :</span><span class="sxs-lookup"><span data-stu-id="5af15-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="5af15-115">Injection de service</span><span class="sxs-lookup"><span data-stu-id="5af15-115">Service injection</span></span>

<span data-ttu-id="5af15-116">Un service peut être injecté dans une vue en utilisant la directive `@inject`.</span><span class="sxs-lookup"><span data-stu-id="5af15-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="5af15-117">`@inject` équivaut à ajouter une propriété à la vue et à remplir la propriété à l’aide de l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="5af15-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="5af15-118">Cette vue affiche une liste d’instances `ToDoItem` et un récapitulatif de statistiques générales.</span><span class="sxs-lookup"><span data-stu-id="5af15-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="5af15-119">Le récapitulatif est rempli avec les données du service `StatisticsService` injecté.</span><span class="sxs-lookup"><span data-stu-id="5af15-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="5af15-120">Ce service est inscrit pour l’injection de dépendances sous `ConfigureServices` dans *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="5af15-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="5af15-121">`StatisticsService` effectue des calculs sur l’ensemble des instances `ToDoItem`, auquel il accède par le biais d’un référentiel :</span><span class="sxs-lookup"><span data-stu-id="5af15-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="5af15-122">Le référentiel de l’exemple utilise une collection en mémoire.</span><span class="sxs-lookup"><span data-stu-id="5af15-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="5af15-123">L’implémentation illustrée ci-dessus (qui s’applique à toutes les données en mémoire) n’est pas recommandée pour les jeux de données volumineux et accessibles à distance.</span><span class="sxs-lookup"><span data-stu-id="5af15-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="5af15-124">L’exemple affiche les données fournies par le modèle lié à la vue et le service injecté dans la vue :</span><span class="sxs-lookup"><span data-stu-id="5af15-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![La vue des tâches To Do affiche le nombre total de tâches et de tâches terminées, la priorité moyenne, et une liste des tâches avec leur niveau de priorité et une valeur booléenne indiquant leur statut.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="5af15-126">Remplissage des données de recherche</span><span class="sxs-lookup"><span data-stu-id="5af15-126">Populating Lookup Data</span></span>

<span data-ttu-id="5af15-127">L’injection dans les vues peut être utile pour remplir certaines options dans les éléments d’interface utilisateur, telles que les listes déroulantes.</span><span class="sxs-lookup"><span data-stu-id="5af15-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="5af15-128">Prenons l’exemple d’un formulaire de profil utilisateur qui comporte des options permettant de spécifier le sexe, l’État et d’autres préférences.</span><span class="sxs-lookup"><span data-stu-id="5af15-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="5af15-129">Pour afficher un formulaire de ce type selon une approche MVC standard, il faudrait que le contrôleur demande l’accès des données aux services pour chacune des options du formulaire, puis qu’il remplisse un modèle ou `ViewBag` avec chaque ensemble d’options à lier.</span><span class="sxs-lookup"><span data-stu-id="5af15-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="5af15-130">Une autre approche consiste à injecter les services directement dans la vue pour obtenir les options.</span><span class="sxs-lookup"><span data-stu-id="5af15-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="5af15-131">Cela réduit la quantité de code requis par le contrôleur, car la logique de construction de cet élément de vue est déplacée dans la vue proprement dite.</span><span class="sxs-lookup"><span data-stu-id="5af15-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="5af15-132">Pour afficher un formulaire de modification de profil, il suffit ainsi au contrôleur de passer l’instance de profil au formulaire :</span><span class="sxs-lookup"><span data-stu-id="5af15-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="5af15-133">Le formulaire HTML utilisé pour mettre à jour ces préférences inclut des listes déroulantes pour trois des propriétés :</span><span class="sxs-lookup"><span data-stu-id="5af15-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Vue de mise à jour du profil, avec un formulaire de saisie d’informations (nom, sexe, État et couleur préférée).](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="5af15-135">Ces listes sont remplies par un service qui a été injecté dans la vue :</span><span class="sxs-lookup"><span data-stu-id="5af15-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="5af15-136">`ProfileOptionsService` est un service au niveau de l’interface utilisateur qui est conçu pour fournir uniquement les données nécessaires dans ce formulaire :</span><span class="sxs-lookup"><span data-stu-id="5af15-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="5af15-137">N’oubliez pas d’inscrire les types à demander par le biais de l’injection de dépendances dans `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5af15-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5af15-138">Un type non inscrit lève une exception au moment de l’exécution, car le fournisseur de services est interrogé en interne par le biais de [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span><span class="sxs-lookup"><span data-stu-id="5af15-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="5af15-139">Substitution de services</span><span class="sxs-lookup"><span data-stu-id="5af15-139">Overriding Services</span></span>

<span data-ttu-id="5af15-140">L’injection de services peut être utilisée pour injecter de nouveaux services, mais également pour substituer des services ayant déjà été injectés dans une page.</span><span class="sxs-lookup"><span data-stu-id="5af15-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="5af15-141">La capture d’écran ci-dessous montre tous les champs disponibles dans la page utilisée dans le premier exemple :</span><span class="sxs-lookup"><span data-stu-id="5af15-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![Menu contextuel IntelliSense pour un symbole @ entré qui affiche les champs Html, Component, StatsService et Url](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="5af15-143">Comme vous pouvez le voir, les champs par défaut incluent `Html`, `Component` et `Url` (mais aussi `StatsService` que nous avons injecté).</span><span class="sxs-lookup"><span data-stu-id="5af15-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="5af15-144">Si vous souhaitez, par exemple, remplacer les HTML Helpers par défaut par les vôtres, vous pouvez facilement le faire en utilisant `@inject` :</span><span class="sxs-lookup"><span data-stu-id="5af15-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="5af15-145">Si vous souhaitez étendre des services existants, vous pouvez simplement utiliser cette technique en héritant de ou en incluant dans un wrapper l’implémentation existante avec la vôtre.</span><span class="sxs-lookup"><span data-stu-id="5af15-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="5af15-146">Voir aussi</span><span class="sxs-lookup"><span data-stu-id="5af15-146">See Also</span></span>

* <span data-ttu-id="5af15-147">Blog de Simon Timms : [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="5af15-147">Simon Timms Blog: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
