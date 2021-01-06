---
title: Autorisation simple dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser l’attribut Authorize pour restreindre l’accès aux contrôleurs et aux actions de ASP.NET Core.
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
uid: security/authorization/simple
ms.openlocfilehash: 1678f1b4af2c65e3b10c66f7ccdbecf19156a834
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97865562"
---
# <a name="simple-authorization-in-aspnet-core"></a>Autorisation simple dans ASP.NET Core

<a name="security-authorization-simple"></a>

L’autorisation dans ASP.NET Core est contrôlée par <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> et ses différents paramètres. Dans sa forme la plus simple, l’application `[Authorize]` de l’attribut à un contrôleur, une action ou une Razor page limite l’accès à ce composant à n’importe quel utilisateur authentifié.

Par exemple, le code suivant limite l’accès à `AccountController` n’importe quel utilisateur authentifié.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Si vous souhaitez appliquer une autorisation à une action plutôt qu’au contrôleur, appliquez l' `AuthorizeAttribute` attribut à l’action elle-même :

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

Désormais, seuls les utilisateurs authentifiés peuvent accéder à la `Logout` fonction.

Vous pouvez également utiliser l' `AllowAnonymous` attribut pour autoriser l’accès par des utilisateurs non authentifiés à des actions individuelles. Par exemple :

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Cela autorise uniquement les utilisateurs authentifiés à, à l' `AccountController` exception de l' `Login` action, qui est accessible par tout le monde, indépendamment de leur état authentifié ou non authentifié/anonyme.

> [!WARNING]
> `[AllowAnonymous]` ignore toutes les instructions d’autorisation. Si vous combinez `[AllowAnonymous]` et n’importe quel `[Authorize]` attribut, les `[Authorize]` attributs sont ignorés. Par exemple, si vous appliquez `[AllowAnonymous]` au niveau du contrôleur, tous les `[Authorize]` attributs du même contrôleur (ou de toute action qu’il contient) sont ignorés.

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-no-locrazor-pages"></a>Autoriser l’attribut et les Razor pages

Le <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ne peut **pas** être appliqué aux Razor gestionnaires de pages. Par exemple, `[Authorize]` ne peut pas être appliqué à `OnGet` , `OnPost` ou à un autre gestionnaire de page. Envisagez d’utiliser un contrôleur ASP.NET Core MVC pour les pages avec des exigences d’autorisation différentes pour différents gestionnaires.

Les deux approches suivantes peuvent être utilisées pour appliquer l’autorisation aux Razor méthodes du gestionnaire de page :

_ Utiliser des pages distinctes pour les gestionnaires de pages nécessitant une autorisation différente. Déplacez le contenu partagé dans une ou plusieurs [vues partielles](xref:mvc/views/partial). Dans la mesure du possible, il s’agit de l’approche recommandée.
* Pour le contenu qui doit partager une page commune, écrivez un filtre qui effectue une autorisation dans le cadre de [IAsyncPageFilter. OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A). Le projet GitHub [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) illustre cette approche :
  * [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implémente le filtre d’autorisation :[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]

  * L’attribut [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) est appliqué au `OnGet` Gestionnaire de page : [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]

> [!WARNING]
> L’exemple d’approche [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) ne fait **pas** de * : _ compose avec les attributs d’autorisation appliqués à la page, au modèle de page ou globalement. La composition des attributs d’autorisation entraîne l’exécution de plusieurs fois `AuthorizeAttribute` pour une ou plusieurs `AuthorizeFilter` instances sur la page.
> * Travaillez conjointement avec le reste du système d’authentification et d’autorisation de ASP.NET Core. Vous devez vérifier que l’utilisation de cette approche fonctionne correctement pour votre application.

Il n’est pas prévu de prendre en charge le `AuthorizeAttribute` sur les Razor gestionnaires de page. 
