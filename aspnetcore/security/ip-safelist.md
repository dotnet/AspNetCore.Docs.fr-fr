---
title: Client IP safelier pour ASP.NET Core
author: damienbod
description: Découvrez comment écrire des filtres d’intergiciel ou d’action pour valider des adresses IP distantes par rapport à une liste d’adresses IP approuvées.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
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
uid: security/ip-safelist
ms.openlocfilehash: f89191cff1faa5b55ee9031a6a6d0749a74e43c0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587539"
---
# <a name="client-ip-safelist-for-aspnet-core"></a>Client IP safelier pour ASP.NET Core

Par [Damien Bowden](https://twitter.com/damien_bod) et [Tom Dykstra](https://github.com/tdykstra)
 
Cet article présente trois façons d’implémenter une adresse IP safelit (également appelée « liste verte ») dans une application ASP.NET Core. Un exemple d’application associé illustre les trois approches. Vous pouvez utiliser :

* Intergiciel pour vérifier l’adresse IP distante de chaque demande.
* Filtres d’action MVC pour vérifier l’adresse IP distante des demandes pour des contrôleurs ou des méthodes d’action spécifiques.
* Razor Pages filtres pour vérifier l’adresse IP distante des demandes de Razor pages.

Dans chaque cas, une chaîne contenant des adresses IP clientes approuvées est stockée dans un paramètre d’application. Le middleware ou le filtre :

* Analyse la chaîne dans un tableau. 
* Vérifie si l’adresse IP distante existe dans le tableau.

L’accès est autorisé si le tableau contient l’adresse IP. Dans le cas contraire, un code d’état HTTP 403 interdit est retourné.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/ip-safelist/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="ip-address-safelist"></a>Adresse IP safelit

Dans l’exemple d’application, l’adresse IP safelit est :

* Défini par la `AdminSafeList` propriété dans le *appsettings.json* fichier.
* Chaîne délimitée par des points-virgules qui peut contenir des adresses [IPv4 (Internet Protocol version 4)](https://wikipedia.org/wiki/IPv4) et [IPv6 (Internet Protocol version 6)](https://wikipedia.org/wiki/IPv6) .

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

Dans l’exemple précédent, les adresses IPv4 de `127.0.0.1` et `192.168.1.5` et l’adresse IPv6 de bouclage `::1` (format compressé pour `0:0:0:0:0:0:0:1` ) sont autorisées.

## <a name="middleware"></a>Middleware

La `Startup.Configure` méthode ajoute le `AdminSafeListMiddleware` type d’intergiciel (middleware) personnalisé au pipeline de demande de l’application. L’offre safelit est récupérée avec le fournisseur de configuration .NET Core et transmise en tant que paramètre de constructeur.

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

L’intergiciel (middleware) analyse la chaîne dans un tableau et recherche l’adresse IP distante dans le tableau. Si l’adresse IP distante est introuvable, l’intergiciel (middleware) renvoie HTTP 403 interdit. Ce processus de validation est contourné pour les requêtes HTTP d’extraction.

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a>Filtre d’action

Si vous souhaitez un contrôle d’accès piloté par safeli pour des contrôleurs MVC ou des méthodes d’action spécifiques, utilisez un filtre d’action. Par exemple :

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

Dans `Startup.ConfigureServices` , ajoutez le filtre d’action à la collection de filtres Mvc. Dans l’exemple suivant, un `ClientIpCheckActionFilter` filtre d’action est ajouté. Un safelit et une instance d’enregistreur d’événements de console sont passés en tant que paramètres de constructeur.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

Le filtre d’action peut ensuite être appliqué à un contrôleur ou à une méthode d’action avec l’attribut [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) :

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

Dans l’exemple d’application, le filtre d’action est appliqué à la `Get` méthode d’action du contrôleur. Lorsque vous testez l’application en envoyant :

* Une requête HTTP d’extraction, l' `[ServiceFilter]` attribut valide l’adresse IP du client. Si l’accès est autorisé à la `Get` méthode d’action, une variante de la sortie de console suivante est générée par le filtre d’action et la méthode d’action :

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* Un verbe de requête HTTP autre que obtenir, l' `AdminSafeListMiddleware` intergiciel (middleware) valide l’adresse IP du client.

## <a name="razor-pages-filter"></a>Razor Filtre de pages

Si vous souhaitez un contrôle d’accès piloté par safelis pour une Razor application pages, utilisez un Razor filtre pages. Par exemple :

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

Dans `Startup.ConfigureServices` , activez le Razor filtre pages en l’ajoutant à la collection de filtres Mvc. Dans l’exemple suivant, un `ClientIpCheckPageFilter` Razor filtre de pages est ajouté. Un safelit et une instance d’enregistreur d’événements de console sont passés en tant que paramètres de constructeur.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

Quand la page d' *index* de l’exemple d’application Razor est demandée, le Razor filtre de pages valide l’adresse IP du client. Le filtre produit une variante de la sortie de console suivante :

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/middleware/index>
* [Filtres d’actions](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
