---
title: Version de compatibilité pour ASP.NET Core MVC
author: rick-anderson
description: Découvrez comment la classe Startup dans ASP.NET Core configure des services et le pipeline de requête de l’application.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/compatibility-version
ms.openlocfilehash: 9123bd70dfee1578912f682faf0520735fd55776
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051289"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="f9217-103">Version de compatibilité pour ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="f9217-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="f9217-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f9217-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f9217-105">La <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> méthode est une absence d’opération pour les applications ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f9217-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="f9217-106">Autrement dit, `SetCompatibilityVersion` l’appel avec n’importe quelle valeur n' <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> a aucun impact sur l’application.</span><span class="sxs-lookup"><span data-stu-id="f9217-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="f9217-107">La version mineure suivante de ASP.NET Core peut fournir une nouvelle `CompatibilityVersion` valeur.</span><span class="sxs-lookup"><span data-stu-id="f9217-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="f9217-108">`CompatibilityVersion``Version_2_0`les valeurs `Version_2_2` sont marquées `[Obsolete(...)]` .</span><span class="sxs-lookup"><span data-stu-id="f9217-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="f9217-109">Consultez [rupture des modifications d’API dans anti-contrefaçon, cors, diagnostics, MVC et routage](https://github.com/aspnet/Announcements/issues/387).</span><span class="sxs-lookup"><span data-stu-id="f9217-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="f9217-110">Cette liste comprend les modifications avec rupture pour les commutateurs de compatibilité.</span><span class="sxs-lookup"><span data-stu-id="f9217-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="f9217-111">Pour voir comment `SetCompatibilityVersion` fonctionne avec les applications ASP.net Core 2. x, sélectionnez la [version ASP.net Core 2,2 de cet article](?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="f9217-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f9217-112">La <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> méthode permet à un ASP.net Core application 2. x d’accepter ou de refuser les modifications de comportement potentiellement en rupture introduites dans ASP.net Core MVC 2,1 ou 2,2.</span><span class="sxs-lookup"><span data-stu-id="f9217-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="f9217-113">Ces changements de comportement potentiellement cassants concernent en général la façon dont le sous-système MVC se comporte et la façon dont **votre code** est appelé par le runtime.</span><span class="sxs-lookup"><span data-stu-id="f9217-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="f9217-114">En acceptant, vous obtenez le comportement le plus récent et le comportement à long terme d’ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f9217-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="f9217-115">Le code suivant définit le mode de compatibilité sur ASP.NET Core 2.2 :</span><span class="sxs-lookup"><span data-stu-id="f9217-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="f9217-116">Nous vous recommandons de tester votre application avec la version la plus récente (`CompatibilityVersion.Latest`).</span><span class="sxs-lookup"><span data-stu-id="f9217-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="f9217-117">Nous pensons que la plupart des applications ne connaîtront pas de changements de comportement cassants avec la version la plus récente.</span><span class="sxs-lookup"><span data-stu-id="f9217-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="f9217-118">Les applications qui appellent `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` sont protégées contre les changements de comportement potentiellement inaltérables introduits dans les versions de ASP.net Core 2.1/2.2 Mvc.</span><span class="sxs-lookup"><span data-stu-id="f9217-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="f9217-119">Cette protection :</span><span class="sxs-lookup"><span data-stu-id="f9217-119">This protection:</span></span>

* <span data-ttu-id="f9217-120">Ne s’applique pas à tous les changements de 2.1 et ultérieur. Elle est destinée aux changements de comportement potentiellement cassants du runtime ASP.NET Core dans le sous-système MVC.</span><span class="sxs-lookup"><span data-stu-id="f9217-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="f9217-121">Ne s’étend pas à ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="f9217-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="f9217-122">La compatibilité par défaut pour les applications ASP.NET Core 2,1 et 2,2 qui n’appellent **pas** `SetCompatibilityVersion` est 2,0.</span><span class="sxs-lookup"><span data-stu-id="f9217-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="f9217-123">Autrement dit, ne pas appeler `SetCompatibilityVersion` revient à appeler `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="f9217-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="f9217-124">Le code suivant définit le mode de compatibilité sur ASP.NET Core 2.2, sauf pour les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="f9217-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="f9217-125">Pour les applications qui rencontrent des changements de comportement cassants, l’utilisation des commutateurs de compatibilité appropriés :</span><span class="sxs-lookup"><span data-stu-id="f9217-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="f9217-126">Vous permet d’utiliser la dernière version et de refuser des changements de comportement cassants spécifiques.</span><span class="sxs-lookup"><span data-stu-id="f9217-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="f9217-127">Vous laisse le temps de mettre à jour votre application pour qu’elle fonctionne avec les derniers changements.</span><span class="sxs-lookup"><span data-stu-id="f9217-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="f9217-128">La documentation <xref:Microsoft.AspNetCore.Mvc.MvcOptions> explique clairement ce qui a changé et pourquoi ces changements représentent une amélioration pour la plupart des utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="f9217-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="f9217-129">Avec ASP.NET Core 3,0, les anciens comportements pris en charge par les commutateurs de compatibilité ont été supprimés.</span><span class="sxs-lookup"><span data-stu-id="f9217-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="f9217-130">Nous pensons que ce sont des changements positifs, qui vont bénéficier à presque tous les utilisateurs.</span><span class="sxs-lookup"><span data-stu-id="f9217-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="f9217-131">En introduisant ces modifications dans 2,1 et 2,2, la plupart des applications peuvent bénéficier de la mise à jour, tandis que d’autres ont du temps.</span><span class="sxs-lookup"><span data-stu-id="f9217-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end