---
title: Extensibilité de la localisation
author: hishamco
description: Découvrez comment étendre les API de localisation dans les applications ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
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
uid: fundamentals/localization-extensibility
ms.openlocfilehash: c41ebc0506a565666963d9b4c70ed73df389c826
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751658"
---
# <a name="localization-extensibility"></a><span data-ttu-id="c5081-103">Extensibilité de la localisation</span><span class="sxs-lookup"><span data-stu-id="c5081-103">Localization Extensibility</span></span>

<span data-ttu-id="c5081-104">Par [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="c5081-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="c5081-105">Cet article :</span><span class="sxs-lookup"><span data-stu-id="c5081-105">This article:</span></span>

* <span data-ttu-id="c5081-106">Répertorie les points d’extensibilité sur les API de localisation.</span><span class="sxs-lookup"><span data-stu-id="c5081-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="c5081-107">Fournit des instructions sur la façon d’étendre la localisation des applications ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c5081-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="c5081-108">Points extensibles dans les API de localisation</span><span class="sxs-lookup"><span data-stu-id="c5081-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="c5081-109">Les API de localisation ASP.NET Core sont conçues pour être extensibles.</span><span class="sxs-lookup"><span data-stu-id="c5081-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="c5081-110">L’extensibilité permet aux développeurs de personnaliser la localisation en fonction de leurs besoins.</span><span class="sxs-lookup"><span data-stu-id="c5081-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="c5081-111">Par exemple, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) a un `POStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="c5081-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="c5081-112">`POStringLocalizer` décrit en détail l’utilisation de la [Localisation d’objet portable](xref:fundamentals/portable-object-localization) pour utiliser des fichiers `PO` afin de stocker des ressources de localisation.</span><span class="sxs-lookup"><span data-stu-id="c5081-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="c5081-113">Cet article répertorie les deux principaux points d’extensibilité fournis par les API de localisation :</span><span class="sxs-lookup"><span data-stu-id="c5081-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="c5081-114">Fournisseurs de culture de localisation</span><span class="sxs-lookup"><span data-stu-id="c5081-114">Localization Culture Providers</span></span>

<span data-ttu-id="c5081-115">Les API de localisation ASP.NET Core incluent quatre fournisseurs par défaut qui peuvent déterminer la culture actuelle d’une requête d’exécution :</span><span class="sxs-lookup"><span data-stu-id="c5081-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="c5081-116">Les fournisseurs précédents sont décrits en détail dans la documentation de l’[intergiciel de localisation](xref:fundamentals/localization).</span><span class="sxs-lookup"><span data-stu-id="c5081-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="c5081-117">Si les fournisseurs par défaut ne répondent pas à vos besoins, créez un fournisseur personnalisé à l’aide de l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="c5081-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="c5081-118">Utiliser CustomRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="c5081-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="c5081-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> fournit un <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> personnalisé qui utilise un délégué simple pour déterminer la culture de localisation actuelle :</span><span class="sxs-lookup"><span data-stu-id="c5081-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="c5081-120">Utiliser une nouvelle implémentation de RequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="c5081-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="c5081-121">Une nouvelle implémentation de <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> peut être créée pour déterminer les informations de culture de requête à partir d’une source personnalisée.</span><span class="sxs-lookup"><span data-stu-id="c5081-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="c5081-122">Par exemple, la source personnalisée peut être un fichier de configuration ou une base de données.</span><span class="sxs-lookup"><span data-stu-id="c5081-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="c5081-123">L’exemple suivant montre `AppSettingsRequestCultureProvider` , qui étend le <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> pour déterminer les informations de culture de la demande à partir de *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="c5081-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a><span data-ttu-id="c5081-124">Ressources de localisation</span><span class="sxs-lookup"><span data-stu-id="c5081-124">Localization resources</span></span>

<span data-ttu-id="c5081-125">La localisation ASP.NET Core fournit <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span><span class="sxs-lookup"><span data-stu-id="c5081-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="c5081-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> est une implémentation de <xref:Microsoft.Extensions.Localization.IStringLocalizer> qui utilise `resx` pour stocker des ressources de localisation.</span><span class="sxs-lookup"><span data-stu-id="c5081-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="c5081-127">Vous n’êtes pas limité à l’utilisation de fichiers `resx`.</span><span class="sxs-lookup"><span data-stu-id="c5081-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="c5081-128">En implémentant `IStringLocalizer`, n’importe quelle source de données peut être utilisée.</span><span class="sxs-lookup"><span data-stu-id="c5081-128">By implementing `IStringLocalizer`, any data source can be used.</span></span>

<span data-ttu-id="c5081-129">Les exemples de projets suivants implémentent <xref:Microsoft.Extensions.Localization.IStringLocalizer> :</span><span class="sxs-lookup"><span data-stu-id="c5081-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="c5081-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="c5081-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="c5081-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="c5081-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="c5081-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="c5081-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)
