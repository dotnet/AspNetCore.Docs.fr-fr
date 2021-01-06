---
title: AspNetCore Microsoft.. app pour ASP.NET Core
author: Rick-Anderson
description: Infrastructure partagée Microsoft. AspNetCore. app
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
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
uid: fundamentals/metapackage-app
ms.openlocfilehash: 225bb1f55c099a476319191726c5a661f9a4893a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057802"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="cabe5-103">Microsoft. AspNetCore. app pour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cabe5-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="cabe5-104">Le ASP.NET Core Framework partagé ( `Microsoft.AspNetCore.App` ) contient des assemblys développés et pris en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cabe5-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="cabe5-105">`Microsoft.AspNetCore.App` est installé lors de l’installation du [Kit de développement logiciel (SDK) .net Core 3,0 ou version ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="cabe5-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="cabe5-106">L' *infrastructure partagée* est l’ensemble d’assemblys (fichiers *. dll* ) qui sont installés sur l’ordinateur et comprend un composant d’exécution et un pack de ciblage.</span><span class="sxs-lookup"><span data-stu-id="cabe5-106">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="cabe5-107">Pour plus d’informations, consultez [Le framework partagé](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="cabe5-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="cabe5-108">Les projets qui ciblent le `Microsoft.NET.Sdk.Web` Kit de développement logiciel (SDK) référencent implicitement le `Microsoft.AspNetCore.App` Framework.</span><span class="sxs-lookup"><span data-stu-id="cabe5-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="cabe5-109">Aucune référence supplémentaire n’est requise pour ces projets :</span><span class="sxs-lookup"><span data-stu-id="cabe5-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="cabe5-110">Framework partagé ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="cabe5-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="cabe5-111">N’inclut pas les dépendances tierces.</span><span class="sxs-lookup"><span data-stu-id="cabe5-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="cabe5-112">Comprend tous les packages pris en charge par l’équipe ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cabe5-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cabe5-113">Cette fonctionnalité nécessite ASP.NET Core 2.x ciblant .NET Core 2.x.</span><span class="sxs-lookup"><span data-stu-id="cabe5-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="cabe5-114">Le [AspNetCore Microsoft.. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [pour ASP.net Core](/dotnet/core/packages#metapackages) :</span><span class="sxs-lookup"><span data-stu-id="cabe5-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="cabe5-115">N’inclut pas les dépendances tierces sauf pour [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) et [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span><span class="sxs-lookup"><span data-stu-id="cabe5-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="cabe5-116">Ces dépendances tierces sont considérées comme nécessaires pour assurer le fonctionnement des principales fonctionnalités des frameworks.</span><span class="sxs-lookup"><span data-stu-id="cabe5-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="cabe5-117">Inclut tous les packages pris en charge par l’équipe ASP.NET Core, sauf ceux qui contiennent des dépendances tierces (autres que celles mentionnées au-dessus).</span><span class="sxs-lookup"><span data-stu-id="cabe5-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="cabe5-118">Inclut tous les packages pris en charge par l’équipe Entity Framework Core, sauf ceux qui contiennent des dépendances tierces (autres que celles mentionnées au-dessus).</span><span class="sxs-lookup"><span data-stu-id="cabe5-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="cabe5-119">Toutes les fonctionnalités d’ASP.NET Core 2.x et d’Entity Framework Core 2.x sont incluses dans le package `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="cabe5-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="cabe5-120">Les modèles de projet par défaut ciblant ASP.NET Core 2. x utilisent ce package.</span><span class="sxs-lookup"><span data-stu-id="cabe5-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="cabe5-121">Nous recommandons aux applications ciblant ASP.NET Core 2. x et Entity Framework Core 2. x d’utiliser le `Microsoft.AspNetCore.App` Package.</span><span class="sxs-lookup"><span data-stu-id="cabe5-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="cabe5-122">Le numéro de version du métapaquet `Microsoft.AspNetCore.App` représente la version minimale d’ASP.NET Core et la version d’Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="cabe5-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="cabe5-123">L’utilisation du métapackage `Microsoft.AspNetCore.App` offre des restrictions de version qui protègent votre application :</span><span class="sxs-lookup"><span data-stu-id="cabe5-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="cabe5-124">Si un package inclus a une dépendance transitive (non directe) sur un package dans `Microsoft.AspNetCore.App` et que ces numéros de version diffèrent, NuGet génère une erreur.</span><span class="sxs-lookup"><span data-stu-id="cabe5-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="cabe5-125">Les autres packages ajoutés à votre application ne peuvent pas changer la version des packages inclus dans `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="cabe5-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="cabe5-126">La cohérence des versions garantit une expérience fiable.</span><span class="sxs-lookup"><span data-stu-id="cabe5-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="cabe5-127">`Microsoft.AspNetCore.App` a été conçu pour empêcher des combinaisons de versions non testées de bits associés d’être utilisées ensemble dans la même application.</span><span class="sxs-lookup"><span data-stu-id="cabe5-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="cabe5-128">Les applications qui utilisent le métapackage `Microsoft.AspNetCore.App` tirent automatiquement parti du framework partagé ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cabe5-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="cabe5-129">Quand vous utilisez le métapackage `Microsoft.AspNetCore.App`, aucune&mdash; des ressources des packages NuGet ASP.NET Core référencés n’est déployée avec l’application : le framework partagé ASP.NET Core contient ces ressources.</span><span class="sxs-lookup"><span data-stu-id="cabe5-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="cabe5-130">Les ressources présentes dans le framework partagé sont précompilées afin d’améliorer la vitesse de démarrage des applications.</span><span class="sxs-lookup"><span data-stu-id="cabe5-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="cabe5-131">Pour plus d’informations, consultez [Le framework partagé](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="cabe5-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="cabe5-132">Le fichier projet suivant fait référence au « `Microsoft.AspNetCore.App` package » pour ASP.net Core et représente un modèle ASP.NET Core 2,2 standard :</span><span class="sxs-lookup"><span data-stu-id="cabe5-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="cabe5-133">Le balisage précédent représente un modèle ASP.NET Core 2. x classique.</span><span class="sxs-lookup"><span data-stu-id="cabe5-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="cabe5-134">Il ne spécifie pas de numéro de version pour la référence du package `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="cabe5-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="cabe5-135">Lorsque la version n’est pas spécifiée, une version [implicite](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) est spécifiée par le kit SDK, autrement dit, `Microsoft.NET.Sdk.Web`.</span><span class="sxs-lookup"><span data-stu-id="cabe5-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="cabe5-136">Nous vous recommandons de garder la version implicite spécifiée par le kit SDK et de ne pas définir de façon explicite le numéro de version sur la référence de package.</span><span class="sxs-lookup"><span data-stu-id="cabe5-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="cabe5-137">Si vous avez des questions par rapport à cette approche, laissez un commentaire GitHub à la page [Discussion concernant la version implicite de Microsoft.AspNetCore.App](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span><span class="sxs-lookup"><span data-stu-id="cabe5-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="cabe5-138">La version implicite est définie sur `major.minor.0` pour les applications portables.</span><span class="sxs-lookup"><span data-stu-id="cabe5-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="cabe5-139">Le mécanisme de restauration par progression des frameworks partagés exécute l’application sur la dernière version compatible parmi les frameworks partagés installés.</span><span class="sxs-lookup"><span data-stu-id="cabe5-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="cabe5-140">Pour garantir l’utilisation de la même version en développement, test et production, vérifiez que la même version du framework partagé est installée dans tous les environnements.</span><span class="sxs-lookup"><span data-stu-id="cabe5-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="cabe5-141">Pour les applications autonomes, le numéro de version implicite est défini sur le `major.minor.patch` du framework partagé inclus dans le kit SDK installé.</span><span class="sxs-lookup"><span data-stu-id="cabe5-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="cabe5-142">La spécification d’un numéro de version sur la référence `Microsoft.AspNetCore.App` ne garantit **pas** que la version du framework partagé sera choisie.</span><span class="sxs-lookup"><span data-stu-id="cabe5-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="cabe5-143">Par exemple, supposez que la version « 2.2.1 » est spécifiée, mais que « 2.2.3 » est installé.</span><span class="sxs-lookup"><span data-stu-id="cabe5-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="cabe5-144">Dans ce cas, l’application utilisera « 2.2.3 ».</span><span class="sxs-lookup"><span data-stu-id="cabe5-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="cabe5-145">Même si ce n’est pas recommandé, vous pouvez désactiver la restauration par progression (correctif et/ou mineur).</span><span class="sxs-lookup"><span data-stu-id="cabe5-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="cabe5-146">Pour plus d’informations sur la restauration par progression de l’hôte dotnet et sur la configuration de son comportement, consultez [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="cabe5-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="cabe5-147">`<Project Sdk` doit avoir la valeur `Microsoft.NET.Sdk.Web` pour utiliser la version implicite `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="cabe5-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="cabe5-148">Lorsque `<Project Sdk="Microsoft.NET.Sdk">` (sans la fin `.Web`) est utilisé :</span><span class="sxs-lookup"><span data-stu-id="cabe5-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="cabe5-149">L’avertissement suivant est généré :</span><span class="sxs-lookup"><span data-stu-id="cabe5-149">The following warning is generated:</span></span>

  <span data-ttu-id="cabe5-150">*AVERTISSEMENT NU1604 : dépendance de projet Microsoft. AspNetCore. app ne contient pas de limite inférieure inclusive. Incluez une limite inférieure dans la version de dépendance pour garantir la cohérence des résultats de la restauration.*</span><span class="sxs-lookup"><span data-stu-id="cabe5-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="cabe5-151">Il s’agit d’un problème connu avec le kit de développement logiciel (SDK) .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="cabe5-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="cabe5-152">Mettre à jour ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cabe5-152">Update ASP.NET Core</span></span>

<span data-ttu-id="cabe5-153">Le  [métapackage](/dotnet/core/packages#metapackages)`Microsoft.AspNetCore.App` n’est pas un package traditionnel qui est mis à jour à partir de NuGet.</span><span class="sxs-lookup"><span data-stu-id="cabe5-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="cabe5-154">Semblable à `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` représente un runtime partagé avec une sémantique de version spéciale gérée en dehors de NuGet.</span><span class="sxs-lookup"><span data-stu-id="cabe5-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="cabe5-155">Pour plus d’informations, consultez [Packages, métapackages et frameworks](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="cabe5-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="cabe5-156">Pour mettre à jour ASP.NET Core :</span><span class="sxs-lookup"><span data-stu-id="cabe5-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="cabe5-157">Sur les machines de développement et les serveurs de builds, téléchargez et installez le [SDK .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="cabe5-157">On development machines and build servers: Download and install the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="cabe5-158">Sur les serveurs de déploiement, téléchargez et installez le [Runtime .NET Core](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="cabe5-158">On deployment servers: Download and install the [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>

 <span data-ttu-id="cabe5-159">Les applications seront restaurées par progression jusqu’à la version installée la plus récente au moment de leur redémarrage.</span><span class="sxs-lookup"><span data-stu-id="cabe5-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="cabe5-160">Vous n’avez pas besoin de mettre à jour le numéro de version `Microsoft.AspNetCore.App` dans le fichier projet.</span><span class="sxs-lookup"><span data-stu-id="cabe5-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="cabe5-161">Pour plus d’informations, consultez [Restaurer par progression des applications dépendantes du framework](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="cabe5-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="cabe5-162">Si votre application utilisait `Microsoft.AspNetCore.All`, consultez [Migration de Microsoft.AspNetCore.All vers Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span><span class="sxs-lookup"><span data-stu-id="cabe5-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
