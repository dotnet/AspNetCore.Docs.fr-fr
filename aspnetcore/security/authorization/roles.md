---
title: Autorisation basée sur les rôles dans ASP.NET Core
author: rick-anderson
description: Découvrez comment restreindre l’accès au contrôleur et à l’action ASP.NET Core en passant des rôles à l’attribut Authorize.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/roles
ms.openlocfilehash: 0a2e62afebbcda9710ef82857c87cae8af0375fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051159"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="432da-103">Autorisation basée sur les rôles dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="432da-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="432da-104">Lorsqu’une identité est créée, elle peut appartenir à un ou plusieurs rôles.</span><span class="sxs-lookup"><span data-stu-id="432da-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="432da-105">Par exemple, Tracy peut appartenir aux rôles administrateur et utilisateur tandis que Scott ne peut appartenir qu’au rôle d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="432da-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="432da-106">Le mode de création et de gestion de ces rôles dépend du magasin de stockage du processus d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="432da-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="432da-107">Les rôles sont exposés au développeur via la méthode [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) sur la classe [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) .</span><span class="sxs-lookup"><span data-stu-id="432da-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="432da-108">Ajout de vérifications de rôle</span><span class="sxs-lookup"><span data-stu-id="432da-108">Adding role checks</span></span>

<span data-ttu-id="432da-109">Les vérifications d’autorisation basées sur les rôles sont déclaratives. &mdash; le développeur les incorpore dans leur code, sur un contrôleur ou une action au sein d’un contrôleur, en spécifiant les rôles dont l’utilisateur actuel doit être membre pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="432da-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="432da-110">Par exemple, le code suivant limite l’accès à toutes les actions sur le `AdministrationController` aux utilisateurs qui sont membres du `Administrator` rôle :</span><span class="sxs-lookup"><span data-stu-id="432da-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="432da-111">Vous pouvez spécifier plusieurs rôles sous la forme d’une liste séparée par des virgules :</span><span class="sxs-lookup"><span data-stu-id="432da-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="432da-112">Ce contrôleur n’est accessible qu’aux utilisateurs qui sont membres du `HRManager` rôle ou du `Finance` rôle.</span><span class="sxs-lookup"><span data-stu-id="432da-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="432da-113">Si vous appliquez plusieurs attributs, un utilisateur ayant accès doit être membre de tous les rôles spécifiés. l’exemple suivant requiert qu’un utilisateur soit membre du `PowerUser` `ControlPanelUser` rôle et.</span><span class="sxs-lookup"><span data-stu-id="432da-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="432da-114">Vous pouvez limiter l’accès en appliquant des attributs d’autorisation de rôle supplémentaires au niveau de l’action :</span><span class="sxs-lookup"><span data-stu-id="432da-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

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

<span data-ttu-id="432da-115">Dans l’extrait de code précédent, les membres du `Administrator` rôle ou le `PowerUser` rôle peuvent accéder au contrôleur et à l' `SetTime` action, mais seuls les membres du `Administrator` rôle peuvent accéder à l' `ShutDown` action.</span><span class="sxs-lookup"><span data-stu-id="432da-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="432da-116">Vous pouvez également verrouiller un contrôleur tout en autorisant l’accès anonyme et non authentifié aux actions individuelles.</span><span class="sxs-lookup"><span data-stu-id="432da-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

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

<span data-ttu-id="432da-117">Pour :::no-loc(Razor)::: les pages, le `AuthorizeAttribute` peut être appliqué par :</span><span class="sxs-lookup"><span data-stu-id="432da-117">For :::no-loc(Razor)::: Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="432da-118">À l’aide d’une [Convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), ou</span><span class="sxs-lookup"><span data-stu-id="432da-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="432da-119">Application `AuthorizeAttribute` de à l' `PageModel` instance :</span><span class="sxs-lookup"><span data-stu-id="432da-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

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
> <span data-ttu-id="432da-120">Les attributs de filtre, y compris `AuthorizeAttribute` , peuvent uniquement être appliqués à PageModel et ne peuvent pas être appliqués à des méthodes de gestionnaire de page spécifiques.</span><span class="sxs-lookup"><span data-stu-id="432da-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="432da-121">Vérifications des rôles basés sur des stratégies</span><span class="sxs-lookup"><span data-stu-id="432da-121">Policy based role checks</span></span>

<span data-ttu-id="432da-122">Les exigences de rôle peuvent également être exprimées à l’aide de la nouvelle syntaxe de stratégie, où un développeur inscrit une stratégie au démarrage dans le cadre de la configuration du service d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="432da-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="432da-123">Cela se produit normalement dans `ConfigureServices()` dans votre fichier *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="432da-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

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

<span data-ttu-id="432da-124">Les stratégies sont appliquées à l’aide de la `Policy` propriété sur l' `AuthorizeAttribute` attribut :</span><span class="sxs-lookup"><span data-stu-id="432da-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="432da-125">Si vous souhaitez spécifier plusieurs rôles autorisés dans une spécification, vous pouvez les spécifier en tant que paramètres de la `RequireRole` méthode :</span><span class="sxs-lookup"><span data-stu-id="432da-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="432da-126">Cet exemple autorise les utilisateurs qui appartiennent aux `Administrator` `PowerUser` `BackupAdministrator` rôles ou.</span><span class="sxs-lookup"><span data-stu-id="432da-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="432da-127">Ajouter des services de rôle à :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="432da-127">Add Role services to :::no-loc(Identity):::</span></span>

<span data-ttu-id="432da-128">Ajoutez [rôles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) pour ajouter des services de rôle :</span><span class="sxs-lookup"><span data-stu-id="432da-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

