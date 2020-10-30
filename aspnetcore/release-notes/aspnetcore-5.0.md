---
title: Nouveautés de ASP.NET Core 5,0
author: rick-anderson
description: Découvrez les nouvelles fonctionnalités de ASP.NET Core 5,0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
- ':::no-loc(Kestrel):::'
uid: aspnetcore-5.0
ms.openlocfilehash: e9c74f7b45ebcdffc19a0483b4e98ad2f44d5747
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061770"
---
# <a name="whats-new-in-aspnet-core-50"></a><span data-ttu-id="7eada-103">Nouveautés de ASP.NET Core 5,0</span><span class="sxs-lookup"><span data-stu-id="7eada-103">What's new in ASP.NET Core 5.0</span></span>

<span data-ttu-id="7eada-104">Cet article met en évidence les modifications les plus importantes apportées à ASP.NET Core 5,0 avec des liens vers la documentation appropriée.</span><span class="sxs-lookup"><span data-stu-id="7eada-104">This article highlights the most significant changes in ASP.NET Core 5.0 with links to relevant documentation.</span></span>

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a><span data-ttu-id="7eada-105">ASP.NET Core MVC et :::no-loc(Razor)::: améliorations</span><span class="sxs-lookup"><span data-stu-id="7eada-105">ASP.NET Core MVC and :::no-loc(Razor)::: improvements</span></span>

### <a name="model-binding-datetime-as-utc"></a><span data-ttu-id="7eada-106">Date et heure de liaison de modèle UTC</span><span class="sxs-lookup"><span data-stu-id="7eada-106">Model binding DateTime as UTC</span></span>

<span data-ttu-id="7eada-107">La liaison de modèle prend désormais en charge la liaison de chaînes de temps UTC à `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="7eada-107">Model binding now supports binding UTC time strings to `DateTime`.</span></span> <span data-ttu-id="7eada-108">Si la requête contient une chaîne d’heure UTC, la liaison de modèle la lie à un UTC `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="7eada-108">If the request contains a UTC time string, model binding binds it to a UTC `DateTime`.</span></span> <span data-ttu-id="7eada-109">Par exemple, la chaîne de temps suivante est liée au temps universel (UTC) `DateTime` : `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span><span class="sxs-lookup"><span data-stu-id="7eada-109">For example, the following time string is bound the UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span></span>

### <a name="model-binding-and-validation-with-c-9-record-types"></a><span data-ttu-id="7eada-110">Liaison de modèle et validation avec les types d’enregistrements C# 9</span><span class="sxs-lookup"><span data-stu-id="7eada-110">Model binding and validation with C# 9 record types</span></span>

<span data-ttu-id="7eada-111">Les [types d’enregistrements C# 9](/dotnet/csharp/whats-new/csharp-9#record-types) peuvent être utilisés avec la liaison de modèle dans un contrôleur MVC ou une :::no-loc(Razor)::: page.</span><span class="sxs-lookup"><span data-stu-id="7eada-111">[C# 9 record types](/dotnet/csharp/whats-new/csharp-9#record-types) can be used with model binding in an MVC controller or a :::no-loc(Razor)::: Page.</span></span> <span data-ttu-id="7eada-112">Les types d’enregistrements sont un bon moyen de modéliser les données transmises sur le réseau.</span><span class="sxs-lookup"><span data-stu-id="7eada-112">Record types are a good way to model data being transmitted over the network.</span></span>

<span data-ttu-id="7eada-113">Par exemple, le code suivant `PersonController` utilise le `Person` type d’enregistrement avec la liaison de modèle et la validation de formulaire :</span><span class="sxs-lookup"><span data-stu-id="7eada-113">For example, the following `PersonController` uses the `Person` record type with model binding and form validation:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

<span data-ttu-id="7eada-114">Le fichier `Person/Index.cshtml` :</span><span class="sxs-lookup"><span data-stu-id="7eada-114">The `Person/Index.cshtml` file:</span></span>

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a><span data-ttu-id="7eada-115">Améliorations apportées à DynamicRouteValueTransformer</span><span class="sxs-lookup"><span data-stu-id="7eada-115">Improvements to DynamicRouteValueTransformer</span></span>

<span data-ttu-id="7eada-116">ASP.NET Core 3,1 introduit <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> comme un moyen d’utiliser un point de terminaison personnalisé pour sélectionner dynamiquement une action ou une page du contrôleur MVC :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="7eada-116">ASP.NET Core 3.1 introduced <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> as a way to use custom endpoint to dynamically select an MVC controller action or a :::no-loc(Razor)::: page.</span></span> <span data-ttu-id="7eada-117">ASP.NET Core applications 5,0 peuvent passer l’État à un `DynamicRouteValueTransformer` et filtrer l’ensemble des points de terminaison choisis.</span><span class="sxs-lookup"><span data-stu-id="7eada-117">ASP.NET Core 5.0 apps can pass state to a `DynamicRouteValueTransformer` and filter the set of endpoints chosen.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="7eada-118">Divers</span><span class="sxs-lookup"><span data-stu-id="7eada-118">Miscellaneous</span></span>

* <span data-ttu-id="7eada-119">L’attribut [[compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) peut être appliqué aux propriétés sur un :::no-loc(Razor)::: modèle de page.</span><span class="sxs-lookup"><span data-stu-id="7eada-119">The [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute can be applied to properties on a :::no-loc(Razor)::: Page model.</span></span>
* <span data-ttu-id="7eada-120">Les paramètres et les propriétés liés à partir du corps sont considérés comme étant requis par défaut.</span><span class="sxs-lookup"><span data-stu-id="7eada-120">Parameters and properties bound from the body are considered required by default.</span></span> <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a><span data-ttu-id="7eada-121">API web</span><span class="sxs-lookup"><span data-stu-id="7eada-121">Web API</span></span>

### <a name="openapi-specification-on-by-default"></a><span data-ttu-id="7eada-122">Spécification OpenAPI activée par défaut</span><span class="sxs-lookup"><span data-stu-id="7eada-122">OpenAPI Specification on by default</span></span>

<span data-ttu-id="7eada-123">La [spécification openapi](http://spec.openapis.org/oas/v3.0.3) est une norme du secteur pour décrire les API http et les intégrer dans des processus métier complexes ou avec des tiers.</span><span class="sxs-lookup"><span data-stu-id="7eada-123">[OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3) is an industry standard for describing HTTP APIs and integrating them into complex business processes or with third parties.</span></span> <span data-ttu-id="7eada-124">OpenAPI est largement pris en charge par tous les fournisseurs de Cloud et de nombreux registres d’API.</span><span class="sxs-lookup"><span data-stu-id="7eada-124">OpenAPI is widely supported by all cloud providers and many API registries.</span></span> <span data-ttu-id="7eada-125">Les applications qui émettent des documents OpenAPI à partir d’API Web ont une variété de nouvelles opportunités dans lesquelles ces API peuvent être utilisées.</span><span class="sxs-lookup"><span data-stu-id="7eada-125">Apps that emit OpenAPI documents from web APIs have a variety of new opportunities in which those APIs can be used.</span></span> <span data-ttu-id="7eada-126">En partenariat avec les maintenateurs du projet open source [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), le modèle d’API ASP.net Core contient une dépendance NuGet sur [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="7eada-126">In partnership with the maintainers of the open-source project [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), the ASP.NET Core API template contains a NuGet dependency on [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="7eada-127">Swashbuckle est un package NuGet Open source populaire qui émet dynamiquement des documents OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="7eada-127">Swashbuckle is a popular open-source NuGet package that emits OpenAPI documents dynamically.</span></span> <span data-ttu-id="7eada-128">Swashbuckle fait cela en passant par les contrôleurs d’API et en générant le document OpenAPI au moment de l’exécution, ou au moment de la génération à l’aide de l’interface CLI Swashbuckle.</span><span class="sxs-lookup"><span data-stu-id="7eada-128">Swashbuckle does this by introspecting over the API controllers and generating the OpenAPI document at run-time, or at build time using the Swashbuckle CLI.</span></span>

<span data-ttu-id="7eada-129">Dans ASP.NET Core 5,0, les modèles d’API Web activent la prise en charge de OpenAPI par défaut.</span><span class="sxs-lookup"><span data-stu-id="7eada-129">In ASP.NET Core 5.0, the web API templates enable the OpenAPI support by default.</span></span> <span data-ttu-id="7eada-130">Pour désactiver OpenAPI :</span><span class="sxs-lookup"><span data-stu-id="7eada-130">To disable OpenAPI:</span></span>

* <span data-ttu-id="7eada-131">Depuis la ligne de commande :</span><span class="sxs-lookup"><span data-stu-id="7eada-131">From the command line:</span></span>

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* <span data-ttu-id="7eada-132">Dans Visual Studio : décochez la case **activer la prise en charge de openapi** .</span><span class="sxs-lookup"><span data-stu-id="7eada-132">From Visual Studio: Uncheck **Enable OpenAPI support** .</span></span>

<span data-ttu-id="7eada-133">Tous les fichiers *. csproj* créés pour les projets d’API Web contiennent la référence de package NuGet [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) .</span><span class="sxs-lookup"><span data-stu-id="7eada-133">All *.csproj* files created for web API projects contain the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet package reference.</span></span>

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

<span data-ttu-id="7eada-134">Le code généré par le modèle contient du code dans `Startup.ConfigureServices` qui active la génération de documents openapi :</span><span class="sxs-lookup"><span data-stu-id="7eada-134">The template generated code contains code in `Startup.ConfigureServices` that activates OpenAPI document generation:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

<span data-ttu-id="7eada-135">La `Startup.Configure` méthode ajoute l’intergiciel Swashbuckle, qui permet :</span><span class="sxs-lookup"><span data-stu-id="7eada-135">The `Startup.Configure` method adds the Swashbuckle middleware, which enables the:</span></span>

* <span data-ttu-id="7eada-136">Processus de génération de documents.</span><span class="sxs-lookup"><span data-stu-id="7eada-136">Document generation process.</span></span>
* <span data-ttu-id="7eada-137">Page d’interface utilisateur Swagger par défaut en mode de développement.</span><span class="sxs-lookup"><span data-stu-id="7eada-137">Swagger UI page by default in development mode.</span></span>

<span data-ttu-id="7eada-138">Le code généré par le modèle n’exposera pas accidentellement la description de l’API lors de la publication en production.</span><span class="sxs-lookup"><span data-stu-id="7eada-138">The template generated code won't accidentally expose the API's description when publishing to production.</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a><span data-ttu-id="7eada-139">Importation de gestion des API Azure</span><span class="sxs-lookup"><span data-stu-id="7eada-139">Azure API Management Import</span></span>

<span data-ttu-id="7eada-140">Quand ASP.NET Core projets d’API activent OpenAPI, Visual Studio 2019, version 16,8 et ultérieure, publient automatiquement une étape supplémentaire dans le processus de publication.</span><span class="sxs-lookup"><span data-stu-id="7eada-140">When ASP.NET Core API projects enable OpenAPI, the Visual Studio 2019 version 16.8 and later publishing automatically offer an additional step in the publishing flow.</span></span> <span data-ttu-id="7eada-141">Les développeurs qui utilisent la [gestion des API Azure](xref:tutorials/publish-to-azure-api-management-using-vs) ont la possibilité d’importer automatiquement les API dans gestion des API Azure pendant le processus de publication :</span><span class="sxs-lookup"><span data-stu-id="7eada-141">Developers who use [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) have an opportunity to automatically import the APIs into Azure API Management during the publish flow:</span></span>

![Importation et publication de gestion des API Azure](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a><span data-ttu-id="7eada-143">Meilleure expérience de lancement pour les projets d’API Web</span><span class="sxs-lookup"><span data-stu-id="7eada-143">Better launch experience for web API projects</span></span>

<span data-ttu-id="7eada-144">Avec OpenAPI activé par défaut, l’expérience de lancement d’application (F5) pour les développeurs d’API Web augmente considérablement.</span><span class="sxs-lookup"><span data-stu-id="7eada-144">With OpenAPI enabled by default, the app launching experience (F5) for web API developers significantly improves.</span></span> <span data-ttu-id="7eada-145">Avec ASP.NET Core 5,0, le modèle d’API Web est préconfiguré pour charger la page d’interface utilisateur Swagger.</span><span class="sxs-lookup"><span data-stu-id="7eada-145">With ASP.NET Core 5.0, the web API template comes pre-configured to load up the Swagger UI page.</span></span> <span data-ttu-id="7eada-146">La page d’interface utilisateur Swagger fournit la documentation ajoutée pour l’API publiée et permet de tester les API en un seul clic.</span><span class="sxs-lookup"><span data-stu-id="7eada-146">The Swagger UI page provides both the documentation added for the published API, and enables testing the APIs with a single click.</span></span>

![vue Swagger/index.html](~/release-notes/static/swagger-ui-page-rc1.png)

## :::no-loc(Blazor):::

### <a name="performance-improvements"></a><span data-ttu-id="7eada-148">Améliorations des performances</span><span class="sxs-lookup"><span data-stu-id="7eada-148">Performance improvements</span></span>

<span data-ttu-id="7eada-149">Pour .NET 5, nous avons apporté des améliorations significatives aux :::no-loc(Blazor WebAssembly)::: performances de l’exécution avec un focus spécifique sur le rendu de l’interface utilisateur complexe et la SÉRIALISATION JSON.</span><span class="sxs-lookup"><span data-stu-id="7eada-149">For .NET 5, we made significant improvements to :::no-loc(Blazor WebAssembly)::: runtime performance with a specific focus on complex UI rendering and JSON serialization.</span></span> <span data-ttu-id="7eada-150">Dans nos tests de performances, :::no-loc(Blazor WebAssembly)::: dans .net 5, il s’agit de deux à trois fois plus rapides pour la plupart des scénarios.</span><span class="sxs-lookup"><span data-stu-id="7eada-150">In our performance tests, :::no-loc(Blazor WebAssembly)::: in .NET 5 is two to three times faster for most scenarios.</span></span> <span data-ttu-id="7eada-151">Pour plus d’informations, consultez le [Blog ASP.net : ASP.net Core des mises à jour dans .net 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span><span class="sxs-lookup"><span data-stu-id="7eada-151">For more information, see [ASP.NET Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span></span>

### <a name="css-isolation"></a><span data-ttu-id="7eada-152">Isolation CSS</span><span class="sxs-lookup"><span data-stu-id="7eada-152">CSS isolation</span></span>

<span data-ttu-id="7eada-153">:::no-loc(Blazor)::: prend désormais en charge la définition de styles CSS dont la portée est limitée à un composant donné.</span><span class="sxs-lookup"><span data-stu-id="7eada-153">:::no-loc(Blazor)::: now supports defining CSS styles that are scoped to a given component.</span></span> <span data-ttu-id="7eada-154">Les styles CSS spécifiques aux composants permettent de mieux comprendre les styles dans une application et d’éviter les effets secondaires involontaires des styles globaux.</span><span class="sxs-lookup"><span data-stu-id="7eada-154">Component-specific CSS styles make it easier to reason about the styles in an app and to avoid unintentional side effects of global styles.</span></span> <span data-ttu-id="7eada-155">Pour plus d'informations, consultez <xref:blazor/components/css-isolation>.</span><span class="sxs-lookup"><span data-stu-id="7eada-155">For more information, see <xref:blazor/components/css-isolation>.</span></span>

### <a name="new-inputfile-component"></a><span data-ttu-id="7eada-156">Nouveau `InputFile` composant</span><span class="sxs-lookup"><span data-stu-id="7eada-156">New `InputFile` component</span></span>

<span data-ttu-id="7eada-157">Le `InputFile` composant permet de lire un ou plusieurs fichiers sélectionnés par un utilisateur pour le téléchargement.</span><span class="sxs-lookup"><span data-stu-id="7eada-157">The `InputFile` component allows reading one or more files selected by a user for upload.</span></span> <span data-ttu-id="7eada-158">Pour plus d'informations, consultez <xref:blazor/file-uploads>.</span><span class="sxs-lookup"><span data-stu-id="7eada-158">For more information, see <xref:blazor/file-uploads>.</span></span>

### <a name="new-inputradio-and-inputradiogroup-components"></a><span data-ttu-id="7eada-159">Nouveaux `InputRadio` `InputRadioGroup` composants et</span><span class="sxs-lookup"><span data-stu-id="7eada-159">New `InputRadio` and `InputRadioGroup` components</span></span>

<span data-ttu-id="7eada-160">:::no-loc(Blazor)::: a des composants intégrés `InputRadio` et `InputRadioGroup` qui simplifient la liaison de données aux groupes de cases d’option avec validation intégrée.</span><span class="sxs-lookup"><span data-stu-id="7eada-160">:::no-loc(Blazor)::: has built-in `InputRadio` and `InputRadioGroup` components that simplify data binding to radio button groups with integrated validation.</span></span> <span data-ttu-id="7eada-161">Pour plus d'informations, consultez <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="7eada-161">For more information, see <xref:blazor/forms-validation>.</span></span>

### <a name="component-virtualization"></a><span data-ttu-id="7eada-162">Virtualisation de composant</span><span class="sxs-lookup"><span data-stu-id="7eada-162">Component virtualization</span></span>

<span data-ttu-id="7eada-163">Améliorez les performances perçues du rendu des composants à l’aide de la :::no-loc(Blazor)::: prise en charge intégrée de la virtualisation du Framework.</span><span class="sxs-lookup"><span data-stu-id="7eada-163">Improve the perceived performance of component rendering using the :::no-loc(Blazor)::: framework's built-in virtualization support.</span></span> <span data-ttu-id="7eada-164">Pour plus d'informations, consultez <xref:blazor/forms-validation#radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="7eada-164">For more information, see <xref:blazor/forms-validation#radio-buttons>.</span></span>

### <a name="ontoggle-event-support"></a><span data-ttu-id="7eada-165">`ontoggle` prise en charge des événements</span><span class="sxs-lookup"><span data-stu-id="7eada-165">`ontoggle` event support</span></span>

<span data-ttu-id="7eada-166">:::no-loc(Blazor)::: les événements prennent désormais en charge l' `ontoggle` événement DOM.</span><span class="sxs-lookup"><span data-stu-id="7eada-166">:::no-loc(Blazor)::: events now support the `ontoggle` DOM event.</span></span> <span data-ttu-id="7eada-167">Pour plus d'informations, consultez <xref:blazor/components/event-handling#event-argument-types>.</span><span class="sxs-lookup"><span data-stu-id="7eada-167">For more information, see <xref:blazor/components/event-handling#event-argument-types>.</span></span>

### <a name="set-ui-focus-in-no-locblazor-apps"></a><span data-ttu-id="7eada-168">Définir le focus de l’interface utilisateur dans les :::no-loc(Blazor)::: applications</span><span class="sxs-lookup"><span data-stu-id="7eada-168">Set UI focus in :::no-loc(Blazor)::: apps</span></span>

<span data-ttu-id="7eada-169">Utilisez la `FocusAsync` méthode pratique sur les références d’éléments pour définir le focus de l’interface utilisateur sur cet élément.</span><span class="sxs-lookup"><span data-stu-id="7eada-169">Use the `FocusAsync` convenience method on element references to set the UI focus to that element.</span></span> <span data-ttu-id="7eada-170">Pour plus d'informations, consultez <xref:blazor/components/event-handling#focus-an-element>.</span><span class="sxs-lookup"><span data-stu-id="7eada-170">For more information, see <xref:blazor/components/event-handling#focus-an-element>.</span></span>

### <a name="custom-validation-class-attributes"></a><span data-ttu-id="7eada-171">Attributs de classe de validation personnalisée</span><span class="sxs-lookup"><span data-stu-id="7eada-171">Custom validation class attributes</span></span>

<span data-ttu-id="7eada-172">Les noms des classes de validation personnalisées sont utiles lors de l’intégration avec des frameworks CSS, tels que bootstrap.</span><span class="sxs-lookup"><span data-stu-id="7eada-172">Custom validation class names are useful when integrating with CSS frameworks, such as Bootstrap.</span></span> <span data-ttu-id="7eada-173">Pour plus d'informations, consultez <xref:blazor/forms-validation#custom-validation-class-attributes>.</span><span class="sxs-lookup"><span data-stu-id="7eada-173">For more information, see <xref:blazor/forms-validation#custom-validation-class-attributes>.</span></span>

### <a name="iasyncdisposable-support"></a><span data-ttu-id="7eada-174">Support IAsyncDisposable</span><span class="sxs-lookup"><span data-stu-id="7eada-174">IAsyncDisposable support</span></span>

<span data-ttu-id="7eada-175">:::no-loc(Blazor)::: les composants prennent désormais en charge l' <xref:System.IAsyncDisposable> interface pour la version asynchrone des ressources allouées.</span><span class="sxs-lookup"><span data-stu-id="7eada-175">:::no-loc(Blazor)::: components now support the <xref:System.IAsyncDisposable> interface for the asynchronous release of allocated resources.</span></span>

### <a name="javascript-isolation-and-object-references"></a><span data-ttu-id="7eada-176">Isolation JavaScript et références d’objets</span><span class="sxs-lookup"><span data-stu-id="7eada-176">JavaScript isolation and object references</span></span>

<span data-ttu-id="7eada-177">:::no-loc(Blazor)::: active l’isolation JavaScript dans les [modules JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)standard.</span><span class="sxs-lookup"><span data-stu-id="7eada-177">:::no-loc(Blazor)::: enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="7eada-178">Pour plus d'informations, consultez <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="7eada-178">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

### <a name="form-components-support-display-name"></a><span data-ttu-id="7eada-179">Nom complet de la prise en charge des composants de formulaire</span><span class="sxs-lookup"><span data-stu-id="7eada-179">Form components support display name</span></span>

<span data-ttu-id="7eada-180">Les composants intégrés suivants prennent en charge les noms complets avec le `DisplayName` paramètre :</span><span class="sxs-lookup"><span data-stu-id="7eada-180">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* `InputDate`
* `InputNumber`
* `InputSelect`

<span data-ttu-id="7eada-181">Pour plus d'informations, consultez <xref:blazor/forms-validation#display-name-support>.</span><span class="sxs-lookup"><span data-stu-id="7eada-181">For more information, see <xref:blazor/forms-validation#display-name-support>.</span></span>

### <a name="catch-all-route-parameters"></a><span data-ttu-id="7eada-182">Paramètres d’itinéraire de rattrapage</span><span class="sxs-lookup"><span data-stu-id="7eada-182">Catch-all route parameters</span></span>

<span data-ttu-id="7eada-183">Les paramètres d’itinéraire Catch-All, qui capturent les chemins d’accès dans plusieurs limites de dossiers, sont pris en charge dans les composants.</span><span class="sxs-lookup"><span data-stu-id="7eada-183">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="7eada-184">Pour plus d'informations, consultez <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="7eada-184">For more information, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="debugging-improvements"></a><span data-ttu-id="7eada-185">Améliorations du débogage</span><span class="sxs-lookup"><span data-stu-id="7eada-185">Debugging improvements</span></span>

<span data-ttu-id="7eada-186">Le débogage :::no-loc(Blazor WebAssembly)::: des applications est amélioré dans ASP.NET Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="7eada-186">Debugging :::no-loc(Blazor WebAssembly)::: apps is improved in ASP.NET Core 5.0.</span></span> <span data-ttu-id="7eada-187">En outre, le débogage est désormais pris en charge sur Visual Studio pour Mac.</span><span class="sxs-lookup"><span data-stu-id="7eada-187">Additionally, debugging is now supported on Visual Studio for Mac.</span></span> <span data-ttu-id="7eada-188">Pour plus d'informations, consultez <xref:blazor/debug>.</span><span class="sxs-lookup"><span data-stu-id="7eada-188">For more information, see <xref:blazor/debug>.</span></span>

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a><span data-ttu-id="7eada-189">Microsoft :::no-loc(Identity)::: v 2.0 et MSAL v 2.0</span><span class="sxs-lookup"><span data-stu-id="7eada-189">Microsoft :::no-loc(Identity)::: v2.0 and MSAL v2.0</span></span>

<span data-ttu-id="7eada-190">:::no-loc(Blazor)::: la sécurité utilise désormais Microsoft :::no-loc(Identity)::: v 2.0 ( [`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web) et [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI) ) et MSAL v 2.0.</span><span class="sxs-lookup"><span data-stu-id="7eada-190">:::no-loc(Blazor)::: security now uses Microsoft :::no-loc(Identity)::: v2.0 ([`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web) and [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI)) and MSAL v2.0.</span></span> <span data-ttu-id="7eada-191">Pour plus d’informations, consultez les rubriques relatives à la [ :::no-loc(Blazor)::: sécurité et au :::no-loc(Identity)::: nœud](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="7eada-191">For more information, see the topics in the [:::no-loc(Blazor)::: Security and :::no-loc(Identity)::: node](xref:blazor/security/index).</span></span>

### <a name="protected-browser-storage-for-no-locblazor-server"></a><span data-ttu-id="7eada-192">Stockage du navigateur protégé pour :::no-loc(Blazor Server):::</span><span class="sxs-lookup"><span data-stu-id="7eada-192">Protected Browser Storage for :::no-loc(Blazor Server):::</span></span>

<span data-ttu-id="7eada-193">:::no-loc(Blazor Server)::: les applications peuvent désormais utiliser la prise en charge intégrée pour le stockage de l’état de l’application dans le navigateur qui a été protégé contre la falsification à l’aide de la protection des données ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7eada-193">:::no-loc(Blazor Server)::: apps can now use built-in support for storing app state in the browser that has been protected from tampering using ASP.NET Core data protection.</span></span> <span data-ttu-id="7eada-194">Les données peuvent être stockées dans le stockage du navigateur local ou dans un stockage de session.</span><span class="sxs-lookup"><span data-stu-id="7eada-194">Data can be stored in either local browser storage or session storage.</span></span> <span data-ttu-id="7eada-195">Pour plus d'informations, consultez <xref:blazor/state-management>.</span><span class="sxs-lookup"><span data-stu-id="7eada-195">For more information, see <xref:blazor/state-management>.</span></span>

### <a name="no-locblazor-webassembly-prerendering"></a><span data-ttu-id="7eada-196">:::no-loc(Blazor WebAssembly)::: préaffichant</span><span class="sxs-lookup"><span data-stu-id="7eada-196">:::no-loc(Blazor WebAssembly)::: prerendering</span></span>

<span data-ttu-id="7eada-197">L’intégration des composants est améliorée entre les modèles d’hébergement, et les :::no-loc(Blazor WebAssembly)::: applications peuvent désormais prérestituer la sortie sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="7eada-197">Component integration is improved across hosting models, and :::no-loc(Blazor WebAssembly)::: apps can now prerender output on the server.</span></span> <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a><span data-ttu-id="7eada-198">Améliorations de la limitation/liaison</span><span class="sxs-lookup"><span data-stu-id="7eada-198">Trimming/linking improvements</span></span>

<span data-ttu-id="7eada-199">:::no-loc(Blazor WebAssembly)::: effectue le découpage/la liaison de langage intermédiaire au cours d’une génération pour supprimer l’IL inutile des assemblys de sortie de l’application.</span><span class="sxs-lookup"><span data-stu-id="7eada-199">:::no-loc(Blazor WebAssembly)::: performs Intermediate Language (IL) trimming/linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="7eada-200">Avec la sortie de ASP.NET Core 5,0, :::no-loc(Blazor WebAssembly)::: effectue une suppression améliorée avec des options de configuration supplémentaires.</span><span class="sxs-lookup"><span data-stu-id="7eada-200">With the release of ASP.NET Core 5.0, :::no-loc(Blazor WebAssembly)::: performs improved trimming with additional configuration options.</span></span> <span data-ttu-id="7eada-201">Pour plus d’informations, consultez <xref:blazor/host-and-deploy/configure-trimmer> et [options de suppression](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="7eada-201">For more information, see <xref:blazor/host-and-deploy/configure-trimmer> and [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

### <a name="browser-compatibility-analyzer"></a><span data-ttu-id="7eada-202">Analyseur de compatibilité des navigateurs</span><span class="sxs-lookup"><span data-stu-id="7eada-202">Browser compatibility analyzer</span></span>

<span data-ttu-id="7eada-203">:::no-loc(Blazor WebAssembly)::: les applications ciblent la surface d’exposition complète de l’API .NET, mais toutes les API .NET ne sont pas prises en charge sur webassembly en raison des contraintes du bac à sable (sandbox).</span><span class="sxs-lookup"><span data-stu-id="7eada-203">:::no-loc(Blazor WebAssembly)::: apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="7eada-204">Les API non prises en charge sont levées <xref:System.PlatformNotSupportedException> lors de l’exécution sur Webassembly.</span><span class="sxs-lookup"><span data-stu-id="7eada-204">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="7eada-205">Un analyseur de compatibilité de plateforme avertit le développeur lorsque l’application utilise des API qui ne sont pas prises en charge par les plateformes cibles de l’application.</span><span class="sxs-lookup"><span data-stu-id="7eada-205">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="7eada-206">Pour plus d'informations, consultez <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span><span class="sxs-lookup"><span data-stu-id="7eada-206">For more information, see <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="7eada-207">Charger des assemblys en différé</span><span class="sxs-lookup"><span data-stu-id="7eada-207">Lazy load assemblies</span></span>

<span data-ttu-id="7eada-208">:::no-loc(Blazor WebAssembly)::: les performances de démarrage de l’application peuvent être améliorées en différant le chargement de certains assemblys d’application jusqu’à ce qu’ils soient nécessaires.</span><span class="sxs-lookup"><span data-stu-id="7eada-208">:::no-loc(Blazor WebAssembly)::: app startup performance can be improved by deferring the loading of some application assemblies until they're required.</span></span> <span data-ttu-id="7eada-209">Pour plus d'informations, consultez <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="7eada-209">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="updated-globalization-support"></a><span data-ttu-id="7eada-210">Support de globalisation mis à jour</span><span class="sxs-lookup"><span data-stu-id="7eada-210">Updated globalization support</span></span>

<span data-ttu-id="7eada-211">La prise en charge de la globalisation est disponible pour :::no-loc(Blazor WebAssembly)::: basée sur les composants internationaux pour Unicode (ICU).</span><span class="sxs-lookup"><span data-stu-id="7eada-211">Globalization support is available for :::no-loc(Blazor WebAssembly)::: based on International Components for Unicode (ICU).</span></span> <span data-ttu-id="7eada-212">Pour plus d'informations, consultez <xref:blazor/globalization-localization>.</span><span class="sxs-lookup"><span data-stu-id="7eada-212">For more information, see <xref:blazor/globalization-localization>.</span></span>

## <a name="grpc"></a><span data-ttu-id="7eada-213">gRPC</span><span class="sxs-lookup"><span data-stu-id="7eada-213">gRPC</span></span>

<span data-ttu-id="7eada-214">De nombreuses améliorations de la préformation ont été apportées dans [gRPC](https://grpc.io/).</span><span class="sxs-lookup"><span data-stu-id="7eada-214">Many preformance improvements have been made in [gRPC](https://grpc.io/).</span></span> <span data-ttu-id="7eada-215">Pour plus d’informations, consultez [améliorations des performances de gRPC dans .net 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span><span class="sxs-lookup"><span data-stu-id="7eada-215">For more information, see [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span></span>

<span data-ttu-id="7eada-216">Pour plus d’informations sur les gRPC, consultez <xref:grpc/index> .</span><span class="sxs-lookup"><span data-stu-id="7eada-216">For more gRPC information, see <xref:grpc/index>.</span></span>

## :::no-loc(SignalR):::

<span data-ttu-id="7eada-217">:::no-loc(SignalR)::: Les filtres de concentrateur, appelés pipelines Hub dans ASP.NET :::no-loc(SignalR)::: , sont une fonctionnalité qui permet au code de s’exécuter avant et après l’appel des méthodes de concentrateur.</span><span class="sxs-lookup"><span data-stu-id="7eada-217">:::no-loc(SignalR)::: Hub filters, called Hub pipelines in ASP.NET :::no-loc(SignalR):::, is a feature that allows code to run before and after Hub methods are called.</span></span> <span data-ttu-id="7eada-218">L’exécution de code avant et après l’appel de méthodes de concentrateur est semblable à la façon dont l’intergiciel peut exécuter du code avant et après une requête HTTP.</span><span class="sxs-lookup"><span data-stu-id="7eada-218">Running code before and after Hub methods are called is similar to how middleware has the ability to run code before and after an HTTP request.</span></span> <span data-ttu-id="7eada-219">Les utilisations courantes incluent la journalisation, la gestion des erreurs et la validation d’argument.</span><span class="sxs-lookup"><span data-stu-id="7eada-219">Common uses include logging, error handling, and argument validation.</span></span>

<span data-ttu-id="7eada-220">Pour plus d’informations, consultez [utiliser des filtres de :::no-loc(SignalR)::: concentrateur dans ASP.net Core ](xref:signalr/hub-filters).</span><span class="sxs-lookup"><span data-stu-id="7eada-220">For more information, see [Use hub filters in ASP.NET Core :::no-loc(SignalR):::](xref:signalr/hub-filters).</span></span>

### <a name="no-locsignalr-parallel-hub-invocations"></a><span data-ttu-id="7eada-221">:::no-loc(SignalR)::: appels de concentrateur parallèles</span><span class="sxs-lookup"><span data-stu-id="7eada-221">:::no-loc(SignalR)::: parallel hub invocations</span></span>

<span data-ttu-id="7eada-222">ASP.NET Core :::no-loc(SignalR)::: est désormais en capacité de gérer les appels de concentrateur parallèle.</span><span class="sxs-lookup"><span data-stu-id="7eada-222">ASP.NET Core :::no-loc(SignalR)::: is now capable of handling parallel hub invocations.</span></span> <span data-ttu-id="7eada-223">Le comportement par défaut peut être modifié pour permettre aux clients d’appeler plusieurs méthodes de concentrateur à la fois :</span><span class="sxs-lookup"><span data-stu-id="7eada-223">The default behavior can be changed to allow clients to invoke more than one hub method at a time:</span></span>

[!code-csharp[](~/release-notes/sample/Startup:::no-loc(SignalR):::hubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a><span data-ttu-id="7eada-224">Ajout de la prise en charge de Messagepack dans :::no-loc(SignalR)::: java client</span><span class="sxs-lookup"><span data-stu-id="7eada-224">Added Messagepack support in :::no-loc(SignalR)::: Java client</span></span>

<span data-ttu-id="7eada-225">Un nouveau package, [com. Microsoft. signalr. messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), ajoute la prise en charge de messagepack au :::no-loc(SignalR)::: client Java.</span><span class="sxs-lookup"><span data-stu-id="7eada-225">A new package, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adds MessagePack support to the :::no-loc(SignalR)::: Java client.</span></span> <span data-ttu-id="7eada-226">Pour utiliser le protocole MessagePack Hub, ajoutez `.withHubProtocol(new MessagePackHubProtocol())` au générateur de connexions :</span><span class="sxs-lookup"><span data-stu-id="7eada-226">To use the MessagePack hub protocol, add `.withHubProtocol(new MessagePackHubProtocol())` to the connection builder:</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update :::no-loc(SignalR)::: code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## :::no-loc(Kestrel):::

* <span data-ttu-id="7eada-227">Points de terminaison rechargeables via la configuration : :::no-loc(Kestrel)::: peut détecter les modifications apportées à la configuration passée à [ :::no-loc(Kestrel):::ServerOptions.Configurer](xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.Core.:::no-loc(Kestrel):::ServerOptions.Configure%2A) et les dissocier des points de terminaison existants et les lier aux nouveaux points de terminaison sans nécessiter le redémarrage de l’application lorsque le `reloadOnChange` paramètre a la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="7eada-227">Reloadable endpoints via configuration: :::no-loc(Kestrel)::: can detect changes to configuration passed to [:::no-loc(Kestrel):::ServerOptions.Configure](xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.Core.:::no-loc(Kestrel):::ServerOptions.Configure%2A) and unbind from existing endpoints and bind to new endpoints without requiring an application restart when the `reloadOnChange` parameter is `true`.</span></span> <span data-ttu-id="7eada-228">Par défaut, lorsque <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> vous utilisez ou <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , est :::no-loc(Kestrel)::: lié à la sous-section de configuration [« :::no-loc(Kestrel)::: »](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) avec l' `reloadOnChange` option activé.</span><span class="sxs-lookup"><span data-stu-id="7eada-228">By default when using <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> or <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, :::no-loc(Kestrel)::: binds to the [":::no-loc(Kestrel):::"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) configuration subsection with `reloadOnChange` enabled.</span></span> <span data-ttu-id="7eada-229">Les applications doivent réussir `reloadOnChange: true` lors `:::no-loc(Kestrel):::ServerOptions.Configure` d’un appel manuel pour accéder aux points de terminaison rechargeables.</span><span class="sxs-lookup"><span data-stu-id="7eada-229">Apps must pass `reloadOnChange: true` when calling `:::no-loc(Kestrel):::ServerOptions.Configure` manually to get reloadable endpoints.</span></span>
* <span data-ttu-id="7eada-230">Améliorations des en-têtes de réponse HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7eada-230">HTTP/2 response headers improvements.</span></span> <span data-ttu-id="7eada-231">Pour plus d’informations, consultez [améliorations des performances](#performance-improvements) dans la section suivante.</span><span class="sxs-lookup"><span data-stu-id="7eada-231">For more information, see [Performance improvements](#performance-improvements) in the next section.</span></span>
* <span data-ttu-id="7eada-232">Prise en charge des types de points de terminaison supplémentaires dans le transport de Sockets : ajout à la nouvelle API introduite dans <xref:System.Net.Sockets?displayProperty=nameWithType> , le transport par défaut des sockets dans [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) permet la liaison aux descripteurs de fichiers existants et aux sockets de domaine UNIX.</span><span class="sxs-lookup"><span data-stu-id="7eada-232">Support for additional endpoints types in the sockets transport: Adding to the new API introduced in <xref:System.Net.Sockets?displayProperty=nameWithType>, the sockets default transport in [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) allows binding to both existing file handles and Unix domain sockets.</span></span> <span data-ttu-id="7eada-233">La prise en charge de la liaison aux descripteurs de fichiers existants permet l’utilisation de l' `Systemd` intégration existante sans nécessiter le `libuv` transport.</span><span class="sxs-lookup"><span data-stu-id="7eada-233">Support for binding to existing file handles enables using the existing `Systemd` integration without requiring the `libuv` transport.</span></span>
* <span data-ttu-id="7eada-234">Décodage d’en-tête personnalisé dans :::no-loc(Kestrel)::: : les applications peuvent spécifier celles <xref:System.Text.Encoding> à utiliser pour interpréter les en-têtes entrants en fonction du nom d’en-tête au lieu de la valeur par défaut UTF-8.</span><span class="sxs-lookup"><span data-stu-id="7eada-234">Custom header decoding in :::no-loc(Kestrel):::: Apps can specify which <xref:System.Text.Encoding> to use to interpret incoming headers based on the header name instead of defaulting to UTF-8.</span></span> <span data-ttu-id="7eada-235">Définissez l'option </span><span class="sxs-lookup"><span data-stu-id="7eada-235">Set the</span></span> <!--<xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions.RequestHeaderEncodingSelector> --> <span data-ttu-id="7eada-236">`Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions.RequestHeaderEncodingSelector` propriété permettant de spécifier l’encodage à utiliser :</span><span class="sxs-lookup"><span data-stu-id="7eada-236">`Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions.RequestHeaderEncodingSelector` property to specify which encoding to use:</span></span>
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.Configure:::no-loc(Kestrel):::(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a><span data-ttu-id="7eada-237">:::no-loc(Kestrel)::: options spécifiques au point de terminaison via la configuration</span><span class="sxs-lookup"><span data-stu-id="7eada-237">:::no-loc(Kestrel)::: endpoint-specific options via configuration</span></span>

<span data-ttu-id="7eada-238">Une prise en charge a été ajoutée pour la configuration des :::no-loc(Kestrel)::: options spécifiques aux points de terminaison par [configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7eada-238">Support has been added for configuring :::no-loc(Kestrel):::’s endpoint-specific options via [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="7eada-239">Les configurations spécifiques aux points de terminaison incluent les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7eada-239">The endpoint-specific configurations includes the:</span></span>

* <span data-ttu-id="7eada-240">Protocoles HTTP utilisés</span><span class="sxs-lookup"><span data-stu-id="7eada-240">HTTP protocols used</span></span>
* <span data-ttu-id="7eada-241">Protocoles TLS utilisés</span><span class="sxs-lookup"><span data-stu-id="7eada-241">TLS protocols used</span></span>
* <span data-ttu-id="7eada-242">Certificat sélectionné</span><span class="sxs-lookup"><span data-stu-id="7eada-242">Certificate selected</span></span>
* <span data-ttu-id="7eada-243">Mode de certificat client</span><span class="sxs-lookup"><span data-stu-id="7eada-243">Cient certificate mode</span></span>

<span data-ttu-id="7eada-244">La configuration permet de spécifier le certificat qui est sélectionné en fonction du nom de serveur spécifié.</span><span class="sxs-lookup"><span data-stu-id="7eada-244">Configuration allows specifying which certificate is selected based on the specified server name.</span></span> <span data-ttu-id="7eada-245">Le nom du serveur fait partie de l’extension de Indication du nom du serveur (SNI) au protocole TLS, comme indiqué par le client.</span><span class="sxs-lookup"><span data-stu-id="7eada-245">The server name is part of the Server Name Indication (SNI) extension to the TLS protocol as indicated by the client.</span></span> <span data-ttu-id="7eada-246">:::no-loc(Kestrel):::la configuration de prend également en charge un préfixe générique dans le nom d’hôte.</span><span class="sxs-lookup"><span data-stu-id="7eada-246">:::no-loc(Kestrel):::'s configuration also supports a wildcard prefix in the host name.</span></span>

<span data-ttu-id="7eada-247">L’exemple suivant montre comment spécifier un point de terminaison spécifique à l’aide d’un fichier de configuration :</span><span class="sxs-lookup"><span data-stu-id="7eada-247">The following example shows how to specify endpoint-specific using a configuration file:</span></span>

```json
{
  ":::no-loc(Kestrel):::": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

<span data-ttu-id="7eada-248">Indication du nom du serveur (SNI) est une extension TLS pour inclure un domaine virtuel dans le cadre de la négociation SSL.</span><span class="sxs-lookup"><span data-stu-id="7eada-248">Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="7eada-249">Cela signifie que le nom de domaine virtuel, ou un nom d’hôte, peut être utilisé pour identifier le point de terminaison réseau.</span><span class="sxs-lookup"><span data-stu-id="7eada-249">What this effectively means is that the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="7eada-250">Améliorations des performances</span><span class="sxs-lookup"><span data-stu-id="7eada-250">Performance improvements</span></span>

### <a name="http2"></a><span data-ttu-id="7eada-251">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7eada-251">HTTP/2</span></span>

* <span data-ttu-id="7eada-252">Réductions significatives des allocations dans le chemin de code HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="7eada-252">Significant reductions in allocations in the HTTP/2 code path.</span></span>
* <span data-ttu-id="7eada-253">Prise en charge de la [compression dynamique HPack](https://tools.ietf.org/html/rfc7541) des en-têtes de réponse http/2 dans [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="7eada-253">Support for [HPack dynamic compression](https://tools.ietf.org/html/rfc7541) of HTTP/2 response headers in [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="7eada-254">Pour plus d’informations, consultez [taille de table d’en-tête](xref:fundamentals/servers/kestrel#header-table-size) et [HPACK : la fonctionnalité de désinstallation sans assistance (fonctionnalité) de http/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span><span class="sxs-lookup"><span data-stu-id="7eada-254">For more information, see [Header table size](xref:fundamentals/servers/kestrel#header-table-size) and [HPACK: the silent killer (feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span></span>
* <span data-ttu-id="7eada-255">Envoi de trames PING HTTP/2 : HTTP/2 est un mécanisme permettant d’envoyer des trames PING pour s’assurer qu’une connexion inactive est toujours fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="7eada-255">Sending HTTP/2 PING frames: HTTP/2 has a mechanism for sending PING frames to ensure an idle connection is still functional.</span></span> <span data-ttu-id="7eada-256">Garantir une connexion viable est particulièrement utile lorsque vous travaillez avec des flux à long terme qui sont souvent inactifs, mais qui ne voient que l’activité de façon intermittente, par exemple, les flux gRPC.</span><span class="sxs-lookup"><span data-stu-id="7eada-256">Ensuring a viable connection is especially useful when working with long-lived streams that are often idle but only intermittently see activity, for example, gRPC streams.</span></span> <span data-ttu-id="7eada-257">Les applications peuvent envoyer des images PING périodiques dans [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) en définissant des limites sur <xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions> :</span><span class="sxs-lookup"><span data-stu-id="7eada-257">Apps can send periodic PING frames in [:::no-loc(Kestrel):::](xref:fundamentals/servers/kestrel) by setting limits on <xref:Microsoft.AspNetCore.Server.:::no-loc(Kestrel):::.:::no-loc(Kestrel):::ServerOptions>:</span></span>

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.Configure:::no-loc(Kestrel):::(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a><span data-ttu-id="7eada-258">Containers</span><span class="sxs-lookup"><span data-stu-id="7eada-258">Containers</span></span>

<span data-ttu-id="7eada-259">Avant .NET 5,0, la génération et la publication d’un *fichier dockerfile* pour une application ASP.net Core nécessitait l’extraction de l’ensemble des kit SDK .net Core et de l’image ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="7eada-259">Prior to .NET 5.0, building and publishing a *Dockerfile* for an ASP.NET Core app required pulling the entire .NET Core SDK and the ASP.NET Core image.</span></span> <span data-ttu-id="7eada-260">Avec cette version, l’extraction des octets des images du SDK est réduite et les octets tirés pour l’image ASP.NET Core sont en grande partie éliminés.</span><span class="sxs-lookup"><span data-stu-id="7eada-260">With this release, pulling the SDK images bytes is reduced and the bytes pulled for the ASP.NET Core image is largely eliminated.</span></span> <span data-ttu-id="7eada-261">Pour plus d’informations, consultez [ce commentaire de problème GitHub](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span><span class="sxs-lookup"><span data-stu-id="7eada-261">For more information, see [this GitHub issue comment](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="7eada-262">Authentification et autorisation</span><span class="sxs-lookup"><span data-stu-id="7eada-262">Authentication and authorization</span></span>

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a><span data-ttu-id="7eada-263">Azure Active Directory l’authentification auprès de Microsoft. :::no-loc(Identity)::: . Internet</span><span class="sxs-lookup"><span data-stu-id="7eada-263">Azure Active Directory authentication with Microsoft.:::no-loc(Identity):::.Web</span></span>

<span data-ttu-id="7eada-264">Les modèles de projet ASP.NET Core s’intègrent désormais <xref:Microsoft.:::no-loc(Identity):::.Web?displayProperty=fullName> à pour gérer l’authentification avec le [répertoire d’activité Azure](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="7eada-264">The ASP.NET Core project templates now integrate with <xref:Microsoft.:::no-loc(Identity):::.Web?displayProperty=fullName> to handle authentication with [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span></span> <span data-ttu-id="7eada-265">[Microsoft. :::no-loc(Identity)::: . Le package Web](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web/) fournit les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7eada-265">The [Microsoft.:::no-loc(Identity):::.Web package](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web/) provides:</span></span>

* <span data-ttu-id="7eada-266">Une meilleure expérience pour l’authentification via Azure AD.</span><span class="sxs-lookup"><span data-stu-id="7eada-266">A better experience for authentication through Azure AD.</span></span>
* <span data-ttu-id="7eada-267">Un moyen plus simple d’accéder aux ressources Azure pour le compte de vos utilisateurs, y compris [Microsoft Graph](/graph/overview).</span><span class="sxs-lookup"><span data-stu-id="7eada-267">An easier way to access Azure resources on behalf of your users, including [Microsoft Graph](/graph/overview).</span></span> <span data-ttu-id="7eada-268">Consultez le [Microsoft. :::no-loc(Identity)::: Exemple Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), qui commence par une connexion de base et passe par une architecture mutualisée, à l’aide d’API Azure, à l’aide d’Microsoft Graph et à la protection de vos propres API.</span><span class="sxs-lookup"><span data-stu-id="7eada-268">See the [Microsoft.:::no-loc(Identity):::.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), which starts with a basic login and advances through multi-tenancy, using Azure APIs, using Microsoft Graph, and protecting your own APIs.</span></span> <span data-ttu-id="7eada-269">`Microsoft.:::no-loc(Identity):::.Web` est disponible avec .NET 5.</span><span class="sxs-lookup"><span data-stu-id="7eada-269">`Microsoft.:::no-loc(Identity):::.Web` is available alongside .NET 5.</span></span>

### <a name="allow-anonymous-access-to-an-endpoint"></a><span data-ttu-id="7eada-270">Autoriser l’accès anonyme à un point de terminaison</span><span class="sxs-lookup"><span data-stu-id="7eada-270">Allow anonymous access to an endpoint</span></span>

<span data-ttu-id="7eada-271">La `AllowAnonymous` méthode d’extension autorise un accès anonyme à un point de terminaison :</span><span class="sxs-lookup"><span data-stu-id="7eada-271">The `AllowAnonymous` extension method allows anonymous access to an endpoint:</span></span>

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a><span data-ttu-id="7eada-272">Gestion personnalisée des échecs d’autorisation</span><span class="sxs-lookup"><span data-stu-id="7eada-272">Custom handling of authorization failures</span></span>

<span data-ttu-id="7eada-273">La gestion personnalisée des échecs d’autorisation est désormais plus facile avec la nouvelle interface [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) appelée par l' [intergiciel (middleware](xref:fundamentals/middleware/index)) [d’autorisation](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) .</span><span class="sxs-lookup"><span data-stu-id="7eada-273">Custom handling of authorization failures is now easier with the new [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) interface that is invoked by the [authorization](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="7eada-274">L’implémentation par défaut reste la même, mais un gestionnaire personnalisé peut être inscrit dans [injection de dépendances, qui autorise les réponses HTTP personnalisées en fonction de la raison de l’échec de l’autorisation.</span><span class="sxs-lookup"><span data-stu-id="7eada-274">The default implementation remains the same, but a custom handler can be registered in [Dependency injection, which allows custom HTTP responses based on why authorization failed.</span></span> <span data-ttu-id="7eada-275">Consultez [cet exemple](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) qui illustre l’utilisation de `IAuthorizationMiddlewareResultHandler` .</span><span class="sxs-lookup"><span data-stu-id="7eada-275">See [this sample](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) that demonstrates usage of the `IAuthorizationMiddlewareResultHandler`.</span></span>

### <a name="authorization-when-using-endpoint-routing"></a><span data-ttu-id="7eada-276">Autorisation lors de l’utilisation du routage de point de terminaison</span><span class="sxs-lookup"><span data-stu-id="7eada-276">Authorization when using endpoint routing</span></span>

<span data-ttu-id="7eada-277">L’autorisation lors de l’utilisation du routage de point de terminaison reçoit désormais le au `HttpContext` lieu de l’instance de point de terminaison.</span><span class="sxs-lookup"><span data-stu-id="7eada-277">Authorization when using endpoint routing now receives the `HttpContext` rather than the endpoint instance.</span></span> <span data-ttu-id="7eada-278">Cela permet à l’intergiciel (middleware) d’autorisation d’accéder au `RouteData` et à d’autres propriétés de `HttpContext` qui n’étaient pas accessibles via la <xref:Microsoft.AspNetCore.Http.Endpoint> classe.</span><span class="sxs-lookup"><span data-stu-id="7eada-278">This allows the authorization middleware to access the `RouteData` and other properties of the `HttpContext` that were not accessible though the <xref:Microsoft.AspNetCore.Http.Endpoint> class.</span></span> <span data-ttu-id="7eada-279">Le point de terminaison peut être extrait du contexte à l’aide du [contexte. GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span><span class="sxs-lookup"><span data-stu-id="7eada-279">The endpoint can be fetched from the context using [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span></span>

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a><span data-ttu-id="7eada-280">Contrôle d’accès en fonction du rôle avec l’authentification Kerberos et LDAP sur Linux</span><span class="sxs-lookup"><span data-stu-id="7eada-280">Role-based access control with Kerberos authentication and LDAP on Linux</span></span>

<span data-ttu-id="7eada-281">Voir [authentification Kerberos et contrôle d’accès en fonction du rôle (RBAC)](xref:security/authentication/windowsauth#rbac)</span><span class="sxs-lookup"><span data-stu-id="7eada-281">See [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac)</span></span>

## <a name="api-improvements"></a><span data-ttu-id="7eada-282">Améliorations des API</span><span class="sxs-lookup"><span data-stu-id="7eada-282">API improvements</span></span>

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a><span data-ttu-id="7eada-283">Méthodes d’extension JSON pour HttpRequest et HttpResponse</span><span class="sxs-lookup"><span data-stu-id="7eada-283">JSON extension methods for HttpRequest and HttpResponse</span></span>

<span data-ttu-id="7eada-284">Les données JSON peuvent être lues et écrites dans à partir d’un `HttpRequest` et `HttpResponse` à l’aide des <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> méthodes d’extension et nouvelles `WriteAsJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="7eada-284">JSON data can be read and written to from an `HttpRequest` and `HttpResponse` using the new <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> and `WriteAsJsonAsync` extension methods.</span></span> <span data-ttu-id="7eada-285">Ces méthodes d’extension utilisent l' [System.Text.Jssur](xref:System.Text.Json) le sérialiseur pour gérer les données JSON.</span><span class="sxs-lookup"><span data-stu-id="7eada-285">These extension methods use the [System.Text.Json](xref:System.Text.Json) serializer to handle the JSON data.</span></span> <span data-ttu-id="7eada-286">La nouvelle `HasJsonContentType` méthode d’extension peut également vérifier si une demande a un type de contenu JSON.</span><span class="sxs-lookup"><span data-stu-id="7eada-286">The new `HasJsonContentType` extension method can also check if a request has a JSON content type.</span></span>

<span data-ttu-id="7eada-287">Les méthodes d’extension JSON peuvent être combinées avec le [routage de point de terminaison](xref:fundamentals/routing) pour créer des API JSON dans un style de programmation que nous appelons \* **router vers le code** _.</span><span class="sxs-lookup"><span data-stu-id="7eada-287">The JSON extension methods can be combined with [endpoint routing](xref:fundamentals/routing) to create JSON APIs in a style of programming we call \* **route to code** _.</span></span> <span data-ttu-id="7eada-288">C’est une nouvelle option pour les développeurs qui souhaitent créer des API JSON de base de façon légère.</span><span class="sxs-lookup"><span data-stu-id="7eada-288">It is a new option for developers who want to create basic JSON APIs in a lightweight way.</span></span> <span data-ttu-id="7eada-289">Par exemple, une application Web qui n’a que quelques points de terminaison peut choisir d’utiliser l’itinéraire vers du code plutôt que les fonctionnalités complètes de ASP.NET Core MVC :</span><span class="sxs-lookup"><span data-stu-id="7eada-289">For example, a web app that has only a handful of endpoints might choose to use route to code rather than the full functionality of ASP.NET Core MVC:</span></span>

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

<span data-ttu-id="7eada-290">Pour plus d’informations sur les nouvelles méthodes d’extension JSON et sur l’acheminement vers du code, consultez [ce .net Show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span><span class="sxs-lookup"><span data-stu-id="7eada-290">For more information on the new JSON extension methods and route to code, see [this .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span></span>

### <a name="systemdiagnosticsactivity"></a><span data-ttu-id="7eada-291">System. Diagnostics. Activity</span><span class="sxs-lookup"><span data-stu-id="7eada-291">System.Diagnostics.Activity</span></span>

<span data-ttu-id="7eada-292">Le format par défaut pour <xref:System.Diagnostics.Activity?displayProperty=fullName> Now est désormais le format W3C.</span><span class="sxs-lookup"><span data-stu-id="7eada-292">The default format for <xref:System.Diagnostics.Activity?displayProperty=fullName> now defaults to the W3C format.</span></span> <span data-ttu-id="7eada-293">Par défaut, la prise en charge du traçage distribué dans ASP.NET Core interopérable avec davantage d’infrastructures.</span><span class="sxs-lookup"><span data-stu-id="7eada-293">This makes distributed tracing support in ASP.NET Core interoperable with more frameworks by default.</span></span>

### <a name="frombodyattribute"></a><span data-ttu-id="7eada-294">FromBodyAttribute</span><span class="sxs-lookup"><span data-stu-id="7eada-294">FromBodyAttribute</span></span>

<span data-ttu-id="7eada-295"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> prend en charge la configuration d’une option qui permet à ces paramètres ou propriétés d’être considérés comme facultatifs :</span><span class="sxs-lookup"><span data-stu-id="7eada-295"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> ow supports configuring an option that allows these parameters or properties to be considered optional:</span></span>

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                           MyModel model) {
     ...
     }
```

## <a name="miscellaneous-improvements"></a><span data-ttu-id="7eada-296">Améliorations diverses</span><span class="sxs-lookup"><span data-stu-id="7eada-296">Miscellaneous improvements</span></span>

<span data-ttu-id="7eada-297">Nous avons commencé à appliquer des [Annotations Nullable](/dotnet/csharp/nullable-references#attributes-describe-apis) aux assemblys ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="7eada-297">We’ve started applying [nullable annotations](/dotnet/csharp/nullable-references#attributes-describe-apis) to ASP.NET Core assemblies.</span></span> <span data-ttu-id="7eada-298">Nous prévoyons d’annoter la majeure partie de la surface d’API publique commune de l’infrastructure .NET 5.</span><span class="sxs-lookup"><span data-stu-id="7eada-298">We plan to annotate most of the common public API surface of the .NET 5 framework.</span></span> <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a><span data-ttu-id="7eada-299">Contrôler l’activation de la classe de démarrage</span><span class="sxs-lookup"><span data-stu-id="7eada-299">Control Startup class activation</span></span>

<span data-ttu-id="7eada-300">Une <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> surcharge supplémentaire a été ajoutée, ce qui permet à une application de fournir une méthode de fabrique pour contrôler `Startup` l’activation de la classe.</span><span class="sxs-lookup"><span data-stu-id="7eada-300">An additional <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> overload has been added that lets an app provide a factory method for controlling `Startup` class activation.</span></span> <span data-ttu-id="7eada-301">Le contrôle de `Startup` l’activation de la classe est utile pour passer des paramètres supplémentaires à `Startup` qui sont initialisés avec l’hôte :</span><span class="sxs-lookup"><span data-stu-id="7eada-301">Controlling `Startup` class activation is useful to pass additional parameters to `Startup` that are initialized along with the host:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a><span data-ttu-id="7eada-302">Actualisation automatique avec dotnet Watch</span><span class="sxs-lookup"><span data-stu-id="7eada-302">Auto refresh with dotnet watch</span></span>

<span data-ttu-id="7eada-303">Dans .NET 5, l’exécution de [dotnet Watch](xref:tutorials/dotnet-watch) sur un projet de ASP.net Core lance le navigateur par défaut et actualise automatiquement le navigateur à mesure que des modifications sont apportées au code.</span><span class="sxs-lookup"><span data-stu-id="7eada-303">In .NET 5, running [dotnet watch](xref:tutorials/dotnet-watch) on an ASP.NET Core project both launches the default browser and auto refreshes the browser as changes are made to the code.</span></span> <span data-ttu-id="7eada-304">Cela signifie que vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="7eada-304">This means you can:</span></span>

<span data-ttu-id="7eada-305">_ Ouvrir un projet de ASP.NET Core dans un éditeur de texte.</span><span class="sxs-lookup"><span data-stu-id="7eada-305">_ Open an ASP.NET Core project in a text editor.</span></span>
* <span data-ttu-id="7eada-306">Exécutez `dotnet watch`.</span><span class="sxs-lookup"><span data-stu-id="7eada-306">Run `dotnet watch`.</span></span>
* <span data-ttu-id="7eada-307">Concentrez-vous sur les modifications de code, tandis que les outils gèrent la reconstruction, le redémarrage et le rechargement de l’application.</span><span class="sxs-lookup"><span data-stu-id="7eada-307">Focus on the code changes while the tooling handles rebuilding, restarting, and reloading the app.</span></span>

<span data-ttu-id="7eada-308">Nous espérons que la fonctionnalité d’actualisation automatique de Visual Studio est à l’avenir.</span><span class="sxs-lookup"><span data-stu-id="7eada-308">We hope to bring the auto refresh functionality to Visual Studio in the future.</span></span>

### <a name="console-logger-formatter"></a><span data-ttu-id="7eada-309">Formateur d’enregistreur de console</span><span class="sxs-lookup"><span data-stu-id="7eada-309">Console Logger Formatter</span></span>

<span data-ttu-id="7eada-310">Des améliorations ont été apportées au module fournisseur d’informations de la console dans la `Microsoft.Extensions.Logging` bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="7eada-310">Improvements have been made to the console log provider in the `Microsoft.Extensions.Logging` library.</span></span> <span data-ttu-id="7eada-311">Les développeurs peuvent désormais implémenter un personnalisé `ConsoleFormatter` pour exercer un contrôle total sur la mise en forme et la coloration de la sortie de la console.</span><span class="sxs-lookup"><span data-stu-id="7eada-311">Developers can now implement a custom `ConsoleFormatter` to exercise complete control over formatting and colorization of the console output.</span></span> <span data-ttu-id="7eada-312">Les API de formateur permettent une mise en forme enrichie en implémentant un sous-ensemble des séquences d’échappement VT-100.</span><span class="sxs-lookup"><span data-stu-id="7eada-312">The formatter APIs allow for rich formatting by implementing a subset of the VT-100 escape sequences.</span></span> <span data-ttu-id="7eada-313">VT-100 est pris en charge par la plupart des terminaux modernes.</span><span class="sxs-lookup"><span data-stu-id="7eada-313">VT-100 is supported by most modern terminals.</span></span> <span data-ttu-id="7eada-314">Le journal de la console peut analyser les séquences d’échappement sur des terminaux non pris en charge, ce qui permet aux développeurs de créer un seul formateur pour tous les terminaux.</span><span class="sxs-lookup"><span data-stu-id="7eada-314">The console logger can parse out escape sequences on unsupported terminals allowing developers to author a single formatter for all terminals.</span></span>

### <a name="json-console-logger"></a><span data-ttu-id="7eada-315">Journal de la console JSON</span><span class="sxs-lookup"><span data-stu-id="7eada-315">JSON Console Logger</span></span>

<span data-ttu-id="7eada-316">En plus de la prise en charge des formateurs personnalisés, nous avons également ajouté un formateur JSON intégré qui émet des journaux JSON structurés dans la console.</span><span class="sxs-lookup"><span data-stu-id="7eada-316">In addition to support for custom formatters, we’ve also added a built-in JSON formatter that emits structured JSON logs to the console.</span></span> <span data-ttu-id="7eada-317">Le code suivant montre comment passer du journal par défaut au format JSON :</span><span class="sxs-lookup"><span data-stu-id="7eada-317">The following code shows how to switch from the default logger to JSON:</span></span>

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

<span data-ttu-id="7eada-318">Les messages de journal émis dans la console sont au format JSON :</span><span class="sxs-lookup"><span data-stu-id="7eada-318">Log messages emitted to the console are JSON formatted:</span></span>

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
