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
ms.openlocfilehash: a6ef5a547e6ccba6771cdf892a9636f83d6796b1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053733"
---
# <a name="localization-extensibility"></a>Extensibilité de la localisation

Par [Hisham Bin Ateya](https://github.com/hishamco)

Cet article :

* Répertorie les points d’extensibilité sur les API de localisation.
* Fournit des instructions sur la façon d’étendre la localisation des applications ASP.NET Core.

## <a name="extensible-points-in-localization-apis"></a>Points extensibles dans les API de localisation

Les API de localisation ASP.NET Core sont conçues pour être extensibles. L’extensibilité permet aux développeurs de personnaliser la localisation en fonction de leurs besoins. Par exemple, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) a un `POStringLocalizer`. `POStringLocalizer` décrit en détail l’utilisation de la [Localisation d’objet portable](xref:fundamentals/portable-object-localization) pour utiliser des fichiers `PO` afin de stocker des ressources de localisation.

Cet article répertorie les deux principaux points d’extensibilité fournis par les API de localisation : 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>Fournisseurs de culture de localisation

Les API de localisation ASP.NET Core incluent quatre fournisseurs par défaut qui peuvent déterminer la culture actuelle d’une requête d’exécution :

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

Les fournisseurs précédents sont décrits en détail dans la documentation de l’[intergiciel de localisation](xref:fundamentals/localization). Si les fournisseurs par défaut ne répondent pas à vos besoins, créez un fournisseur personnalisé à l’aide de l’une des approches suivantes :

### <a name="use-customrequestcultureprovider"></a>Utiliser CustomRequestCultureProvider

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> fournit un <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> personnalisé qui utilise un délégué simple pour déterminer la culture de localisation actuelle :

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

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>Utiliser une nouvelle implémentation de RequestCultureProvider

Une nouvelle implémentation de <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> peut être créée pour déterminer les informations de culture de requête à partir d’une source personnalisée. Par exemple, la source personnalisée peut être un fichier de configuration ou une base de données.

L’exemple suivant montre `AppSettingsRequestCultureProvider` , qui étend le <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> pour déterminer les informations de culture de la demande à partir de *appsettings.json* :

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

## <a name="localization-resources"></a>Ressources de localisation

La localisation ASP.NET Core fournit <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>. <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> est une implémentation de <xref:Microsoft.Extensions.Localization.IStringLocalizer> qui utilise `resx` pour stocker des ressources de localisation.

Vous n’êtes pas limité à l’utilisation de fichiers `resx`. En implémentant `IStringLocalized`, n’importe quelle source de données peut être utilisée.

Les exemples de projets suivants implémentent <xref:Microsoft.Extensions.Localization.IStringLocalizer> : 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
