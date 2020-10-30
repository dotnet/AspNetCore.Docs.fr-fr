---
title: Autorisation basée sur les revendications dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ajouter des vérifications de revendications pour l’autorisation dans une application ASP.NET Core.
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
uid: security/authorization/claims
ms.openlocfilehash: d6317da6bca69b4c46d74a2f76d81af4059d1cd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060272"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="48caa-103">Autorisation basée sur les revendications dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48caa-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="48caa-104">Lorsqu’une identité est créée, elle peut se voir attribuer une ou plusieurs revendications émises par un tiers de confiance.</span><span class="sxs-lookup"><span data-stu-id="48caa-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="48caa-105">Une revendication est une paire nom/valeur qui représente l’objet, pas ce que le sujet peut faire.</span><span class="sxs-lookup"><span data-stu-id="48caa-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="48caa-106">Par exemple, vous pouvez avoir une licence de pilote, publiée par une autorité de certification de conduite locale.</span><span class="sxs-lookup"><span data-stu-id="48caa-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="48caa-107">La date de la licence de votre pilote est la date de naissance.</span><span class="sxs-lookup"><span data-stu-id="48caa-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="48caa-108">Dans ce cas, le nom de la revendication est `DateOfBirth` , la valeur de la revendication est la date de naissance, par exemple, `8th June 1970` et l’émetteur est l’autorité de la licence de conduite.</span><span class="sxs-lookup"><span data-stu-id="48caa-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="48caa-109">L’autorisation basée sur les revendications, à son plus simple, vérifie la valeur d’une revendication et autorise l’accès à une ressource en fonction de cette valeur.</span><span class="sxs-lookup"><span data-stu-id="48caa-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="48caa-110">Par exemple, si vous souhaitez accéder à un club nocturne, le processus d’autorisation peut être :</span><span class="sxs-lookup"><span data-stu-id="48caa-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="48caa-111">Le responsable de la sécurité de la porte évalue la valeur de votre revendication de date de naissance et s’il fait confiance à l’émetteur (l’autorité de licence de conduite) avant de vous accorder l’accès.</span><span class="sxs-lookup"><span data-stu-id="48caa-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="48caa-112">Une identité peut contenir plusieurs revendications avec plusieurs valeurs et peut contenir plusieurs revendications du même type.</span><span class="sxs-lookup"><span data-stu-id="48caa-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="48caa-113">Ajout de vérifications de revendications</span><span class="sxs-lookup"><span data-stu-id="48caa-113">Adding claims checks</span></span>

<span data-ttu-id="48caa-114">Les vérifications d’autorisation basées sur les revendications sont déclaratives : le développeur les incorpore dans leur code, sur un contrôleur ou une action au sein d’un contrôleur, en spécifiant les revendications que l’utilisateur actuel doit posséder, et éventuellement la valeur que la revendication doit conserver pour accéder à la ressource demandée.</span><span class="sxs-lookup"><span data-stu-id="48caa-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="48caa-115">Les demandes de revendications sont basées sur des stratégies, le développeur doit créer et inscrire une stratégie exprimant les exigences en matière de revendications.</span><span class="sxs-lookup"><span data-stu-id="48caa-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="48caa-116">Le type de stratégie de revendication le plus simple recherche la présence d’une revendication et ne vérifie pas la valeur.</span><span class="sxs-lookup"><span data-stu-id="48caa-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="48caa-117">Tout d’abord, vous devez créer et inscrire la stratégie.</span><span class="sxs-lookup"><span data-stu-id="48caa-117">First you need to build and register the policy.</span></span> <span data-ttu-id="48caa-118">Cela a lieu dans le cadre de la configuration du service d’autorisation, qui s’inscrit normalement dans `ConfigureServices()` votre fichier *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="48caa-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
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
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

<span data-ttu-id="48caa-119">Dans ce cas, la `EmployeeOnly` stratégie vérifie la présence d’une `EmployeeNumber` revendication sur l’identité actuelle.</span><span class="sxs-lookup"><span data-stu-id="48caa-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="48caa-120">Vous appliquez ensuite la stratégie à l’aide de la `Policy` propriété sur l' `AuthorizeAttribute` attribut pour spécifier le nom de la stratégie ;</span><span class="sxs-lookup"><span data-stu-id="48caa-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="48caa-121">L' `AuthorizeAttribute` attribut peut être appliqué à un contrôleur entier. dans ce cas, seules les identités correspondant à la stratégie seront autorisées à accéder à une action sur le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="48caa-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="48caa-122">Si vous avez un contrôleur qui est protégé par l' `AuthorizeAttribute` attribut, mais que vous souhaitez autoriser l’accès anonyme à des actions spécifiques, vous appliquez l' `AllowAnonymousAttribute` attribut.</span><span class="sxs-lookup"><span data-stu-id="48caa-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

<span data-ttu-id="48caa-123">La plupart des revendications sont accompagnées d’une valeur.</span><span class="sxs-lookup"><span data-stu-id="48caa-123">Most claims come with a value.</span></span> <span data-ttu-id="48caa-124">Vous pouvez spécifier une liste de valeurs autorisées lors de la création de la stratégie.</span><span class="sxs-lookup"><span data-stu-id="48caa-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="48caa-125">L’exemple suivant ne fonctionne que pour les employés dont le matricule d’employé était 1, 2, 3, 4 ou 5.</span><span class="sxs-lookup"><span data-stu-id="48caa-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
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
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="48caa-126">Ajouter une vérification de revendication générique</span><span class="sxs-lookup"><span data-stu-id="48caa-126">Add a generic claim check</span></span>

<span data-ttu-id="48caa-127">Si la valeur de revendication n’est pas une valeur unique ou si une transformation est requise, utilisez [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="48caa-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="48caa-128">Pour plus d’informations, consultez [utiliser une Func pour accomplir une stratégie](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="48caa-128">For more information, see [Use a func to fulfill a policy](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="48caa-129">Évaluation de plusieurs stratégies</span><span class="sxs-lookup"><span data-stu-id="48caa-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="48caa-130">Si vous appliquez plusieurs stratégies à un contrôleur ou à une action, toutes les stratégies doivent réussir avant l’octroi de l’accès.</span><span class="sxs-lookup"><span data-stu-id="48caa-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="48caa-131">Exemple :</span><span class="sxs-lookup"><span data-stu-id="48caa-131">For example:</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

<span data-ttu-id="48caa-132">Dans l’exemple ci-dessus, toute identité qui répond `EmployeeOnly` à la stratégie peut accéder à l' `Payslip` action, car cette stratégie est appliquée sur le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="48caa-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="48caa-133">Toutefois, pour appeler l' `UpdateSalary` action, l’identité doit respecter à la *fois* la `EmployeeOnly` stratégie et la `HumanResources` stratégie.</span><span class="sxs-lookup"><span data-stu-id="48caa-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="48caa-134">Si vous souhaitez des stratégies plus compliquées, telles que la prise de la revendication date de naissance, le calcul d’une ancienneté, le contrôle de l’âge est 21 ou plus, vous devez écrire des [gestionnaires de stratégie personnalisés](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="48caa-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
