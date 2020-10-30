---
title: Autorisation basée sur les rôles dans ASP.NET Core
author: rick-anderson
description: Découvrez comment restreindre l’accès au contrôleur et à l’action ASP.NET Core en passant des rôles à l’attribut Authorize.
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
uid: security/authorization/roles
ms.openlocfilehash: 0a2e62afebbcda9710ef82857c87cae8af0375fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051159"
---
# <a name="role-based-authorization-in-aspnet-core"></a>Autorisation basée sur les rôles dans ASP.NET Core

<a name="security-authorization-role-based"></a>

Lorsqu’une identité est créée, elle peut appartenir à un ou plusieurs rôles. Par exemple, Tracy peut appartenir aux rôles administrateur et utilisateur tandis que Scott ne peut appartenir qu’au rôle d’utilisateur. Le mode de création et de gestion de ces rôles dépend du magasin de stockage du processus d’autorisation. Les rôles sont exposés au développeur via la méthode [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) sur la classe [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .

## <a name="adding-role-checks"></a>Ajout de vérifications de rôle

Les vérifications d’autorisation basées sur les rôles sont déclaratives. &mdash; le développeur les incorpore dans leur code, sur un contrôleur ou une action au sein d’un contrôleur, en spécifiant les rôles dont l’utilisateur actuel doit être membre pour accéder à la ressource demandée.

Par exemple, le code suivant limite l’accès à toutes les actions sur le `AdministrationController` aux utilisateurs qui sont membres du `Administrator` rôle :

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Vous pouvez spécifier plusieurs rôles sous la forme d’une liste séparée par des virgules :

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Ce contrôleur n’est accessible qu’aux utilisateurs qui sont membres du `HRManager` rôle ou du `Finance` rôle.

Si vous appliquez plusieurs attributs, un utilisateur ayant accès doit être membre de tous les rôles spécifiés. l’exemple suivant requiert qu’un utilisateur soit membre du `PowerUser` `ControlPanelUser` rôle et.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Vous pouvez limiter l’accès en appliquant des attributs d’autorisation de rôle supplémentaires au niveau de l’action :

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

Dans l’extrait de code précédent, les membres du `Administrator` rôle ou le `PowerUser` rôle peuvent accéder au contrôleur et à l' `SetTime` action, mais seuls les membres du `Administrator` rôle peuvent accéder à l' `ShutDown` action.

Vous pouvez également verrouiller un contrôleur tout en autorisant l’accès anonyme et non authentifié aux actions individuelles.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

Pour Razor les pages, le `AuthorizeAttribute` peut être appliqué par :

* À l’aide d’une [Convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), ou
* Application `AuthorizeAttribute` de à l' `PageModel` instance :

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> Les attributs de filtre, y compris `AuthorizeAttribute` , peuvent uniquement être appliqués à PageModel et ne peuvent pas être appliqués à des méthodes de gestionnaire de page spécifiques.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Vérifications des rôles basés sur des stratégies

Les exigences de rôle peuvent également être exprimées à l’aide de la nouvelle syntaxe de stratégie, où un développeur inscrit une stratégie au démarrage dans le cadre de la configuration du service d’autorisation. Cela se produit normalement dans `ConfigureServices()` dans votre fichier *Startup.cs* .

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

Les stratégies sont appliquées à l’aide de la `Policy` propriété sur l' `AuthorizeAttribute` attribut :

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Si vous souhaitez spécifier plusieurs rôles autorisés dans une spécification, vous pouvez les spécifier en tant que paramètres de la `RequireRole` méthode :

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

Cet exemple autorise les utilisateurs qui appartiennent aux `Administrator` `PowerUser` `BackupAdministrator` rôles ou.

### <a name="add-role-services-to-no-locidentity"></a>Ajouter des services de rôle à Identity

Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) pour ajouter des services de rôle :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

