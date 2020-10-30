---
title: Migrer à partir de ClaimsPrincipal. Current
author: mjrousos
description: Découvrez comment migrer à partir de ClaimsPrincipal. Current pour récupérer l’identité et les revendications de l’utilisateur authentifié actuel dans ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
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
uid: migration/claimsprincipal-current
ms.openlocfilehash: 3aa0adb299789efbb071cdb934d43832a84cf540
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059765"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrer à partir de ClaimsPrincipal. Current

Dans les projets ASP.NET 4. x, il était courant d’utiliser [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) pour récupérer l’identité et les revendications de l’utilisateur authentifié actuel. Dans ASP.NET Core, cette propriété n’est plus définie. Le code qui dépend de lui doit être mis à jour pour obtenir l’identité de l’utilisateur authentifié actuel par le biais d’un autre moyen.

## <a name="context-specific-state-instead-of-static-state"></a>État spécifique au contexte au lieu d’un état statique

Lorsque vous utilisez ASP.NET Core, les valeurs de `ClaimsPrincipal.Current` et de `Thread.CurrentPrincipal` ne sont pas définies. Ces propriétés représentent l’état statique, ce qui ASP.NET Core en général évite. Au lieu de cela, ASP.NET Core utilise l' [injection de dépendances](xref:fundamentals/dependency-injection) pour fournir des dépendances telles que l’identité de l’utilisateur actuel. L’obtention de l’identité de l’utilisateur actuel à partir d’DI est également plus testable, car les identités de test peuvent être facilement injectées.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Récupérer l’utilisateur actuel dans une application ASP.NET Core

Il existe plusieurs options pour récupérer les ASP.NET Core de l’utilisateur authentifié actuel à la `ClaimsPrincipal` place de `ClaimsPrincipal.Current` :

* **ControllerBase. utilisateur** . Les contrôleurs MVC peuvent accéder à l’utilisateur authentifié actuel avec leur propriété [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) .
* **HttpContext. User** . Les composants ayant accès au actuel `HttpContext` (intergiciel, par exemple) peuvent obtenir les utilisateurs actuels `ClaimsPrincipal` de [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Passé à partir de l’appelant** . Les bibliothèques sans accès au actuel `HttpContext` sont souvent appelées à partir de contrôleurs ou de composants de middleware et peuvent avoir l’identité de l’utilisateur actuel passée comme argument.
* **IHttpContextAccessor** . Le projet en cours de migration vers ASP.NET Core peut être trop volumineux pour transmettre facilement l’identité de l’utilisateur actuel à tous les emplacements nécessaires. Dans ce cas, [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) peut être utilisé comme solution de contournement. `IHttpContextAccessor` peut accéder au actuel `HttpContext` (s’il en existe un). Si DI est utilisé, consultez <xref:fundamentals/httpcontext> . Une solution à bref terme pour obtenir l’identité de l’utilisateur actuel dans du code qui n’a pas encore été mis à jour pour fonctionner avec l’architecture de l’injection de ASP.NET Core est :

  * Rendez `IHttpContextAccessor` disponible dans le conteneur di en appelant [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) dans `Startup.ConfigureServices` .
  * Obtenir une instance de `IHttpContextAccessor` au cours du démarrage et la stocker dans une variable statique. L’instance est mise à la disposition du code qui récupérait précédemment l’utilisateur actuel à partir d’une propriété statique.
  * Récupérez l’utilisateur actuel `ClaimsPrincipal` à l’aide de `HttpContextAccessor.HttpContext?.User` . Si ce code est utilisé en dehors du contexte d’une requête HTTP, `HttpContext` a la valeur null.

La dernière option, à l’aide d’une `IHttpContextAccessor` instance stockée dans une variable statique, est contraire à la ASP.net Core principe de préférence des dépendances injectées aux dépendances statiques. Envisagez de récupérer `IHttpContextAccessor` des instances à partir de di à la place. Toutefois, une application auxiliaire statique peut être utile lors de la migration d’applications ASP.NET volumineuses existantes qui utilisent `ClaimsPrincipal.Current` .
