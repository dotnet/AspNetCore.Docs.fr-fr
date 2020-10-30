---
title: Utiliser des API ASP.NET Core dans une bibliothèque de classes
author: scottaddie
description: Découvrez comment utiliser des API ASP.NET Core dans une bibliothèque de classes.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
no-loc:
- ':::no-loc(appsettings.json):::'
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: c012658a6f48247af60c8bfd56a7d987f6aa8a68
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061507"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="0a751-103">Utiliser des API ASP.NET Core dans une bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="0a751-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="0a751-104">Par [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="0a751-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="0a751-105">Ce document fournit des conseils pour l’utilisation d’API ASP.NET Core dans une bibliothèque de classes.</span><span class="sxs-lookup"><span data-stu-id="0a751-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="0a751-106">Pour toutes les autres instructions de bibliothèque, consultez l’aide de la [bibliothèque open source](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="0a751-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="0a751-107">Déterminer les versions de ASP.NET Core à prendre en charge</span><span class="sxs-lookup"><span data-stu-id="0a751-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="0a751-108">ASP.NET Core adhère à la [stratégie de prise en charge de .net Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="0a751-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="0a751-109">Consultez la stratégie de prise en charge lors de la détermination des versions de ASP.NET Core à prendre en charge dans une bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="0a751-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="0a751-110">Une bibliothèque doit :</span><span class="sxs-lookup"><span data-stu-id="0a751-110">A library should:</span></span>

* <span data-ttu-id="0a751-111">Prenez le temps de prendre en charge toutes les versions de ASP.NET Core classées en tant que *support à long terme* (LTS).</span><span class="sxs-lookup"><span data-stu-id="0a751-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="0a751-112">Il n’est pas obligatoire de prendre en charge les versions de ASP.NET Core classées en *fin de vie* (EOL).</span><span class="sxs-lookup"><span data-stu-id="0a751-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="0a751-113">À mesure que les versions préliminaires des ASP.NET Core sont disponibles, les modifications avec rupture sont publiées dans le référentiel GitHub [ASPNET/announcements](https://github.com/aspnet/Announcements/issues) .</span><span class="sxs-lookup"><span data-stu-id="0a751-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="0a751-114">Les tests de compatibilité des bibliothèques peuvent être effectués lors du développement des fonctionnalités de l’infrastructure.</span><span class="sxs-lookup"><span data-stu-id="0a751-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="0a751-115">Utiliser le ASP.NET Core Framework partagé</span><span class="sxs-lookup"><span data-stu-id="0a751-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="0a751-116">Avec la sortie de .NET Core 3,0, de nombreux ASP.NET Core assemblys ne sont plus publiés dans NuGet en tant que packages.</span><span class="sxs-lookup"><span data-stu-id="0a751-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="0a751-117">Au lieu de cela, les assemblys sont inclus dans le `Microsoft.AspNetCore.App` Framework partagé, qui est installé avec le kit SDK .net Core et les programmes d’installation du Runtime.</span><span class="sxs-lookup"><span data-stu-id="0a751-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="0a751-118">Pour obtenir la liste des packages qui ne sont plus publiés, consultez [Supprimer les références de package obsolètes](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="0a751-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="0a751-119">À compter de .NET Core 3,0, les projets qui utilisent le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK) MSBuild référencent implicitement le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="0a751-120">Les projets qui utilisent le `Microsoft.NET.Sdk` `Microsoft.NET.Sdk.:::no-loc(Razor):::` Kit de développement logiciel (SDK) ou doivent référencer ASP.net Core pour utiliser des API ASP.net core dans le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.:::no-loc(Razor):::` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="0a751-121">Pour référencer ASP.NET Core, ajoutez l' `<FrameworkReference>` élément suivant à votre fichier projet :</span><span class="sxs-lookup"><span data-stu-id="0a751-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="0a751-122">Le fait de référencer ASP.NET Core de cette manière est pris en charge uniquement pour les projets ciblant .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="0a751-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-no-locblazor-extensibility"></a><span data-ttu-id="0a751-123">Inclure l' :::no-loc(Blazor)::: extensibilité</span><span class="sxs-lookup"><span data-stu-id="0a751-123">Include :::no-loc(Blazor)::: extensibility</span></span>

<span data-ttu-id="0a751-124">:::no-loc(Blazor)::: prend en charge webassembly (WASM) et les [modèles d’hébergement](xref:blazor/hosting-models)de serveur.</span><span class="sxs-lookup"><span data-stu-id="0a751-124">:::no-loc(Blazor)::: supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="0a751-125">À moins qu’il y ait une raison spécifique de ne pas le faire, une bibliothèque de [ :::no-loc(Razor)::: composants](xref:blazor/components/index) doit prendre en charge les deux modèles d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="0a751-125">Unless there's a specific reason not to, a [:::no-loc(Razor)::: components](xref:blazor/components/index) library should support both hosting models.</span></span> <span data-ttu-id="0a751-126">Une :::no-loc(Razor)::: bibliothèque de composants doit utiliser le [Kit de développement logiciel (SDK) :::no-loc(Razor)::: Microsoft. net. SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="0a751-126">A :::no-loc(Razor)::: components library must use the [Microsoft.NET.Sdk.:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="0a751-127">Prendre en charge les modèles d’hébergement</span><span class="sxs-lookup"><span data-stu-id="0a751-127">Support both hosting models</span></span>

<span data-ttu-id="0a751-128">Pour prendre en charge la :::no-loc(Razor)::: consommation des composants à partir des [:::no-loc(Blazor Server):::](xref:blazor/hosting-models#blazor-server) projets et [ :::no-loc(Blazor)::: WASM](xref:blazor/hosting-models#blazor-webassembly) , utilisez les instructions suivantes pour votre éditeur.</span><span class="sxs-lookup"><span data-stu-id="0a751-128">To support :::no-loc(Razor)::: component consumption from both [:::no-loc(Blazor Server):::](xref:blazor/hosting-models#blazor-server) and [:::no-loc(Blazor)::: WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a751-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a751-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a751-130">Utilisez le modèle de projet **:::no-loc(Razor)::: bibliothèque de classes** .</span><span class="sxs-lookup"><span data-stu-id="0a751-130">Use the **:::no-loc(Razor)::: Class Library** project template.</span></span> <span data-ttu-id="0a751-131">La case à cocher **pages de prise en charge et vues** du modèle doit être désélectionnée.</span><span class="sxs-lookup"><span data-stu-id="0a751-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a751-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a751-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a751-133">Exécutez la commande suivante dans le terminal intégré :</span><span class="sxs-lookup"><span data-stu-id="0a751-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a751-134">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0a751-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a751-135">Utilisez le modèle de projet **:::no-loc(Razor)::: bibliothèque de classes** .</span><span class="sxs-lookup"><span data-stu-id="0a751-135">Use the **:::no-loc(Razor)::: Class Library** project template.</span></span>

---

<span data-ttu-id="0a751-136">Le projet généré à partir du modèle effectue les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="0a751-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="0a751-137">Cible .NET Standard 2,0.</span><span class="sxs-lookup"><span data-stu-id="0a751-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="0a751-138">Affecte la valeur `:::no-loc(Razor):::LangVersion` à la propriété `3.0`.</span><span class="sxs-lookup"><span data-stu-id="0a751-138">Sets the `:::no-loc(Razor):::LangVersion` property to `3.0`.</span></span> <span data-ttu-id="0a751-139">`3.0` est la valeur par défaut pour .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="0a751-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="0a751-140">Ajoute les références de package suivantes :</span><span class="sxs-lookup"><span data-stu-id="0a751-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="0a751-141">Microsoft. AspNetCore. Components</span><span class="sxs-lookup"><span data-stu-id="0a751-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="0a751-142">Microsoft. AspNetCore. Components. Web</span><span class="sxs-lookup"><span data-stu-id="0a751-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="0a751-143">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0a751-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="0a751-144">Prendre en charge un modèle d’hébergement spécifique</span><span class="sxs-lookup"><span data-stu-id="0a751-144">Support a specific hosting model</span></span>

<span data-ttu-id="0a751-145">La prise en charge d’un modèle d’hébergement unique est beaucoup moins courante :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="0a751-145">It's far less common to support a single :::no-loc(Blazor)::: hosting model.</span></span> <span data-ttu-id="0a751-146">Par exemple, pour prendre en charge :::no-loc(Razor)::: la consommation de composants à partir de [:::no-loc(Blazor Server):::](xref:blazor/hosting-models#blazor-server) projets uniquement :</span><span class="sxs-lookup"><span data-stu-id="0a751-146">As an example, to support :::no-loc(Razor)::: component consumption from [:::no-loc(Blazor Server):::](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="0a751-147">Ciblez .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="0a751-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="0a751-148">Ajoutez un `<FrameworkReference>` élément pour le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="0a751-149">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0a751-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="0a751-150">Pour plus d’informations sur les bibliothèques contenant des :::no-loc(Razor)::: composants, consultez [ASP.net Core les bibliothèques de :::no-loc(Razor)::: classes](xref:blazor/components/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="0a751-150">For more information on libraries containing :::no-loc(Razor)::: components, see [ASP.NET Core :::no-loc(Razor)::: components class libraries](xref:blazor/components/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="0a751-151">Inclure l’extensibilité MVC</span><span class="sxs-lookup"><span data-stu-id="0a751-151">Include MVC extensibility</span></span>

<span data-ttu-id="0a751-152">Cette section décrit les recommandations pour les bibliothèques qui incluent :</span><span class="sxs-lookup"><span data-stu-id="0a751-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="0a751-153">:::no-loc(Razor)::: vues ou :::no-loc(Razor)::: pages</span><span class="sxs-lookup"><span data-stu-id="0a751-153">:::no-loc(Razor)::: views or :::no-loc(Razor)::: Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="0a751-154">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="0a751-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="0a751-155">Composants de vue</span><span class="sxs-lookup"><span data-stu-id="0a751-155">View components</span></span>](#view-components)

<span data-ttu-id="0a751-156">Cette section n’aborde pas le multi-ciblage pour prendre en charge plusieurs versions de MVC.</span><span class="sxs-lookup"><span data-stu-id="0a751-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="0a751-157">Pour obtenir des conseils sur la prise en charge de plusieurs versions de ASP.NET Core, consultez [prise en charge de plusieurs versions de ASP.net Core](#support-multiple-aspnet-core-versions).</span><span class="sxs-lookup"><span data-stu-id="0a751-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="no-locrazor-views-or-no-locrazor-pages"></a><span data-ttu-id="0a751-158">:::no-loc(Razor)::: vues ou :::no-loc(Razor)::: pages</span><span class="sxs-lookup"><span data-stu-id="0a751-158">:::no-loc(Razor)::: views or :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="0a751-159">Un projet qui comprend des [ :::no-loc(Razor)::: affichages](xref:mvc/views/overview) ou des [ :::no-loc(Razor)::: pages](xref:razor-pages/index) doit utiliser le [Kit de développement logiciel (SDK) :::no-loc(Razor)::: Microsoft. net. SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="0a751-159">A project that includes [:::no-loc(Razor)::: views](xref:mvc/views/overview) or [:::no-loc(Razor)::: Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.:::no-loc(Razor)::: SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="0a751-160">Si le projet cible .NET Core 3. x, il requiert :</span><span class="sxs-lookup"><span data-stu-id="0a751-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="0a751-161">`Add:::no-loc(Razor):::SupportForMvc`Propriété MSBuild ayant la valeur `true` .</span><span class="sxs-lookup"><span data-stu-id="0a751-161">An `Add:::no-loc(Razor):::SupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="0a751-162">`<FrameworkReference>`Élément pour le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="0a751-163">Le modèle de projet **:::no-loc(Razor)::: bibliothèque de classes** remplit les conditions précédentes pour les projets ciblant .net Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="0a751-163">The **:::no-loc(Razor)::: Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="0a751-164">Utilisez les instructions suivantes pour votre éditeur.</span><span class="sxs-lookup"><span data-stu-id="0a751-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0a751-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0a751-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0a751-166">Utilisez le modèle de projet **:::no-loc(Razor)::: bibliothèque de classes** .</span><span class="sxs-lookup"><span data-stu-id="0a751-166">Use the **:::no-loc(Razor)::: Class Library** project template.</span></span> <span data-ttu-id="0a751-167">La case à cocher **pages de prise en charge et vues** du modèle doit être activée.</span><span class="sxs-lookup"><span data-stu-id="0a751-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="0a751-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0a751-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0a751-169">Exécutez la commande suivante dans le terminal intégré :</span><span class="sxs-lookup"><span data-stu-id="0a751-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0a751-170">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="0a751-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0a751-171">Aucun modèle de projet n’est pris en charge pour le moment.</span><span class="sxs-lookup"><span data-stu-id="0a751-171">No project template support at this time.</span></span>

---

<span data-ttu-id="0a751-172">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0a751-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="0a751-173">Si le projet cible .NET Standard à la place, une référence de package [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) est requise.</span><span class="sxs-lookup"><span data-stu-id="0a751-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="0a751-174">Le `Microsoft.AspNetCore.Mvc` package a été déplacé dans le Framework partagé dans ASP.NET Core 3,0 et n’est donc plus publié.</span><span class="sxs-lookup"><span data-stu-id="0a751-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="0a751-175">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0a751-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="0a751-176">Tag Helpers</span><span class="sxs-lookup"><span data-stu-id="0a751-176">Tag Helpers</span></span>

<span data-ttu-id="0a751-177">Le kit de développement logiciel (SDK) doit être utilisé dans un projet qui comprend des [tag Helper](xref:mvc/views/tag-helpers/intro) `Microsoft.NET.Sdk` .</span><span class="sxs-lookup"><span data-stu-id="0a751-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="0a751-178">Si vous ciblez .NET Core 3. x, ajoutez un `<FrameworkReference>` élément pour le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="0a751-179">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0a751-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="0a751-180">Si vous ciblez .NET Standard (pour prendre en charge les versions antérieures à ASP.NET Core 3. x), ajoutez une référence de package à [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::)</span><span class="sxs-lookup"><span data-stu-id="0a751-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::).</span></span> <span data-ttu-id="0a751-181">Le `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::` package a été déplacé dans le Framework partagé et n’est donc plus publié.</span><span class="sxs-lookup"><span data-stu-id="0a751-181">The `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="0a751-182">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0a751-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="0a751-183">Composants de vue</span><span class="sxs-lookup"><span data-stu-id="0a751-183">View components</span></span>

<span data-ttu-id="0a751-184">Un projet qui comprend des [composants de vue](xref:mvc/views/view-components) doit utiliser le kit de `Microsoft.NET.Sdk` développement logiciel (SDK).</span><span class="sxs-lookup"><span data-stu-id="0a751-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="0a751-185">Si vous ciblez .NET Core 3. x, ajoutez un `<FrameworkReference>` élément pour le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="0a751-186">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0a751-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="0a751-187">Si vous ciblez .NET Standard (pour prendre en charge les versions antérieures à ASP.NET Core 3. x), ajoutez une référence de package à [Microsoft. AspNetCore. Mvc. ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="0a751-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="0a751-188">Le `Microsoft.AspNetCore.Mvc.ViewFeatures` package a été déplacé dans le Framework partagé et n’est donc plus publié.</span><span class="sxs-lookup"><span data-stu-id="0a751-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="0a751-189">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0a751-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="0a751-190">Prendre en charge plusieurs versions de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a751-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="0a751-191">Le multi-ciblage est nécessaire pour créer une bibliothèque qui prend en charge plusieurs variantes de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0a751-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="0a751-192">Imaginez un scénario dans lequel une bibliothèque d’aide pour les balises doit prendre en charge les variantes de ASP.NET Core suivantes :</span><span class="sxs-lookup"><span data-stu-id="0a751-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="0a751-193">ASP.NET Core 2,1 ciblant .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="0a751-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="0a751-194">ASP.NET Core 2. x ciblant .NET Core 2. x</span><span class="sxs-lookup"><span data-stu-id="0a751-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="0a751-195">ASP.NET Core 3. x ciblant .NET Core 3. x</span><span class="sxs-lookup"><span data-stu-id="0a751-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="0a751-196">Le fichier projet suivant prend en charge ces variantes via la `TargetFrameworks` propriété :</span><span class="sxs-lookup"><span data-stu-id="0a751-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="0a751-197">Avec le fichier projet précédent :</span><span class="sxs-lookup"><span data-stu-id="0a751-197">With the preceding project file:</span></span>

* <span data-ttu-id="0a751-198">Le `Markdig` package est ajouté pour tous les consommateurs.</span><span class="sxs-lookup"><span data-stu-id="0a751-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="0a751-199">Référence à [Microsoft. AspNetCore. Mvc. :::no-loc(Razor)::: ](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::)</span><span class="sxs-lookup"><span data-stu-id="0a751-199">A reference to [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::)</span></span> <span data-ttu-id="0a751-200">est ajouté pour les consommateurs ciblant .NET Framework 4.6.1 ou version ultérieure ou .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="0a751-200">is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="0a751-201">La version 2.1.0 du package fonctionne avec ASP.NET Core 2,2 en raison d’une compatibilité descendante.</span><span class="sxs-lookup"><span data-stu-id="0a751-201">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="0a751-202">L’infrastructure partagée est référencée pour les consommateurs ciblant .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="0a751-202">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="0a751-203">Le `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::` package est inclus dans le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-203">The `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::` package is included in the shared framework.</span></span>

<span data-ttu-id="0a751-204">.NET Standard 2,0 peut également être ciblé au lieu de cibler à la fois .NET Core 2,1 et .NET Framework 4.6.1 :</span><span class="sxs-lookup"><span data-stu-id="0a751-204">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="0a751-205">Avec le fichier projet précédent, les avertissements suivants existent :</span><span class="sxs-lookup"><span data-stu-id="0a751-205">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="0a751-206">Étant donné que la bibliothèque ne contient que des balises d’aide, il est plus simple de cibler les plateformes spécifiques sur lesquelles ASP.NET Core s’exécute : .NET Core et .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="0a751-206">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="0a751-207">Les tag helpers ne peuvent pas être utilisés par d’autres frameworks cibles compatibles .NET Standard 2,0 tels que Unity, UWP et Xamarin.</span><span class="sxs-lookup"><span data-stu-id="0a751-207">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="0a751-208">L’utilisation de .NET Standard 2.0 dans .NET Framework entraîne certains problèmes qui ont été résolus dans .NET Framework 4.7.2.</span><span class="sxs-lookup"><span data-stu-id="0a751-208">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="0a751-209">Vous pouvez améliorer l’expérience des consommateurs à l’aide de .NET Framework 4.6.1 à 4.7.1 en ciblant .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="0a751-209">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="0a751-210">Si votre bibliothèque doit appeler des API spécifiques à la plateforme, ciblez des implémentations .NET spécifiques au lieu de .NET Standard.</span><span class="sxs-lookup"><span data-stu-id="0a751-210">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="0a751-211">Pour plus d’informations, consultez [multi-ciblage](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="0a751-211">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="0a751-212">Utiliser une API qui n’a pas changé</span><span class="sxs-lookup"><span data-stu-id="0a751-212">Use an API that hasn't changed</span></span>

<span data-ttu-id="0a751-213">Imaginez un scénario dans lequel vous effectuez la mise à niveau d’une bibliothèque middleware de .NET Core 2,2 vers 3,0.</span><span class="sxs-lookup"><span data-stu-id="0a751-213">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="0a751-214">Les API de ASP.NET Core middleware utilisées dans la bibliothèque n’ont pas changé entre ASP.NET Core 2,2 et 3,0.</span><span class="sxs-lookup"><span data-stu-id="0a751-214">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="0a751-215">Pour continuer à prendre en charge la bibliothèque middleware dans .NET Core 3,0, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="0a751-215">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="0a751-216">Suivez les [instructions de la bibliothèque standard](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="0a751-216">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="0a751-217">Ajoutez une référence de package pour chaque package NuGet de l’API si l’assembly correspondant n’existe pas dans le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-217">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="0a751-218">Utiliser une API qui a changé</span><span class="sxs-lookup"><span data-stu-id="0a751-218">Use an API that changed</span></span>

<span data-ttu-id="0a751-219">Imaginez un scénario dans lequel vous effectuez la mise à niveau d’une bibliothèque de .NET Core 2,2 vers .NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0a751-219">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="0a751-220">Une API ASP.NET Core en cours d’utilisation dans la bibliothèque a une [modification avec rupture](/dotnet/core/compatibility/breaking-changes) dans ASP.net Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0a751-220">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="0a751-221">Déterminez si la bibliothèque peut être réécrite de façon à ne pas utiliser l’API endommagée dans toutes les versions.</span><span class="sxs-lookup"><span data-stu-id="0a751-221">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="0a751-222">Si vous pouvez réécrire la bibliothèque, faites-le et continuez à cibler un Framework cible antérieur (par exemple, .NET Standard 2,0 ou .NET Framework 4.6.1) avec des références de package.</span><span class="sxs-lookup"><span data-stu-id="0a751-222">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="0a751-223">Si vous ne pouvez pas réécrire la bibliothèque, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="0a751-223">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="0a751-224">Ajoutez une cible pour .NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0a751-224">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="0a751-225">Ajoutez un `<FrameworkReference>` élément pour le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-225">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="0a751-226">Utilisez la [directive de préprocesseur #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) avec le symbole de Framework cible approprié pour compiler le code de façon conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="0a751-226">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="0a751-227">Par exemple, les lectures et écritures synchrones sur les flux de requête et de réponse HTTP sont désactivées par défaut à partir de ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0a751-227">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="0a751-228">ASP.NET Core 2,2 prend en charge le comportement synchrone par défaut.</span><span class="sxs-lookup"><span data-stu-id="0a751-228">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="0a751-229">Prenons l’exemple d’une bibliothèque middleware dans laquelle les lectures et écritures synchrones doivent être activées là où l’e/s se produit.</span><span class="sxs-lookup"><span data-stu-id="0a751-229">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="0a751-230">La bibliothèque doit encadrer le code pour activer les fonctionnalités synchrones dans la directive de préprocesseur appropriée.</span><span class="sxs-lookup"><span data-stu-id="0a751-230">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="0a751-231">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0a751-231">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="0a751-232">Utiliser une API introduite dans 3,0</span><span class="sxs-lookup"><span data-stu-id="0a751-232">Use an API introduced in 3.0</span></span>

<span data-ttu-id="0a751-233">Imaginez que vous souhaitez utiliser une API ASP.NET Core qui a été introduite dans ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0a751-233">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="0a751-234">Posez-vous les questions suivantes :</span><span class="sxs-lookup"><span data-stu-id="0a751-234">Consider the following questions:</span></span>

1. <span data-ttu-id="0a751-235">La bibliothèque a-t-elle besoin de la nouvelle API ?</span><span class="sxs-lookup"><span data-stu-id="0a751-235">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="0a751-236">La bibliothèque peut-elle implémenter cette fonctionnalité de manière différente ?</span><span class="sxs-lookup"><span data-stu-id="0a751-236">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="0a751-237">Si la bibliothèque requiert un fonctionnement de l’API et qu’il n’existe aucun moyen de l’implémenter de manière descendante :</span><span class="sxs-lookup"><span data-stu-id="0a751-237">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="0a751-238">Cible .NET Core 3. x uniquement.</span><span class="sxs-lookup"><span data-stu-id="0a751-238">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="0a751-239">Ajoutez un `<FrameworkReference>` élément pour le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-239">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="0a751-240">Si la bibliothèque peut implémenter la fonctionnalité d’une autre façon :</span><span class="sxs-lookup"><span data-stu-id="0a751-240">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="0a751-241">Ajoutez .NET Core 3. x comme Framework cible.</span><span class="sxs-lookup"><span data-stu-id="0a751-241">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="0a751-242">Ajoutez un `<FrameworkReference>` élément pour le Framework partagé.</span><span class="sxs-lookup"><span data-stu-id="0a751-242">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="0a751-243">Utilisez la [directive de préprocesseur #if](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) avec le symbole de Framework cible approprié pour compiler le code de façon conditionnelle.</span><span class="sxs-lookup"><span data-stu-id="0a751-243">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="0a751-244">Par exemple, le tag Helper suivant utilise l' <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduite dans ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="0a751-244">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="0a751-245">Les consommateurs ciblant .NET Core 3,0 exécutent le chemin de code défini par le `NETCOREAPP3_0` symbole de Framework cible.</span><span class="sxs-lookup"><span data-stu-id="0a751-245">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="0a751-246">Le type de paramètre du constructeur de tag Helper devient <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> pour .net Core 2,1 et .NET Framework 4.6.1 Consumers.</span><span class="sxs-lookup"><span data-stu-id="0a751-246">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="0a751-247">Cette modification était nécessaire, car ASP.NET Core 3,0 marquée `IHostingEnvironment` comme obsolète et recommandée `IWebHostEnvironment` comme remplacement.</span><span class="sxs-lookup"><span data-stu-id="0a751-247">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="0a751-248">Le fichier de projet multi-ciblé suivant prend en charge ce scénario d’assistance de balise :</span><span class="sxs-lookup"><span data-stu-id="0a751-248">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="0a751-249">Utiliser une API supprimée de l’infrastructure partagée</span><span class="sxs-lookup"><span data-stu-id="0a751-249">Use an API removed from the shared framework</span></span>

<span data-ttu-id="0a751-250">Pour utiliser un assembly ASP.NET Core qui a été supprimé de l’infrastructure partagée, ajoutez la référence de package appropriée.</span><span class="sxs-lookup"><span data-stu-id="0a751-250">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="0a751-251">Pour obtenir la liste des packages supprimés de l’infrastructure partagée dans ASP.NET Core 3,0, consultez [Supprimer les références de package obsolètes](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="0a751-251">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="0a751-252">Par exemple, pour ajouter le client d’API Web :</span><span class="sxs-lookup"><span data-stu-id="0a751-252">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="0a751-253">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="0a751-253">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [<span data-ttu-id="0a751-254">Prise en charge des implémentations de .NET</span><span class="sxs-lookup"><span data-stu-id="0a751-254">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="0a751-255">Stratégies de support .NET</span><span class="sxs-lookup"><span data-stu-id="0a751-255">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
