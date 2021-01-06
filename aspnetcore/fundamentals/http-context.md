---
title: Accéder à HttpContext dans ASP.NET Core
author: coderandhiker
description: Découvrez comment accéder à HttpContext dans ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
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
uid: fundamentals/httpcontext
ms.openlocfilehash: d5ada72bb03cd74cf77c31450e5eafb9c2841049
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506992"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="b198b-103">Accéder à HttpContext dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b198b-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="b198b-104">ASP.NET Core les applications accessibles par `HttpContext` le biais de l' <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface et de son implémentation par défaut <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> .</span><span class="sxs-lookup"><span data-stu-id="b198b-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="b198b-105">Il est seulement nécessaire d’utiliser `IHttpContextAccessor` quand vous avez besoin d’accéder à `HttpContext` à l’intérieur d’un service.</span><span class="sxs-lookup"><span data-stu-id="b198b-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-no-locrazor-pages"></a><span data-ttu-id="b198b-106">Utiliser HttpContext à partir de Razor pages</span><span class="sxs-lookup"><span data-stu-id="b198b-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="b198b-107">Les Razor pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposent la <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> propriété :</span><span class="sxs-lookup"><span data-stu-id="b198b-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-no-locrazor-view"></a><span data-ttu-id="b198b-108">Utiliser HttpContext à partir d’une Razor vue</span><span class="sxs-lookup"><span data-stu-id="b198b-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="b198b-109">Razorles vues exposent `HttpContext` directement le via une propriété [ Razor page. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) sur la vue.</span><span class="sxs-lookup"><span data-stu-id="b198b-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="b198b-110">L’exemple suivant récupère le nom d’utilisateur actuel dans une application intranet à l’aide de l’authentification Windows :</span><span class="sxs-lookup"><span data-stu-id="b198b-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="b198b-111">Utiliser HttpContext à partir d’un contrôleur</span><span class="sxs-lookup"><span data-stu-id="b198b-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="b198b-112">Les contrôleurs exposent la propriété [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) :</span><span class="sxs-lookup"><span data-stu-id="b198b-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="b198b-113">Utiliser HttpContext à partir d’un intergiciel</span><span class="sxs-lookup"><span data-stu-id="b198b-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="b198b-114">Lorsque vous travaillez avec des composants d’intergiciel personnalisés, `HttpContext` est transmis à la méthode `Invoke` ou `InvokeAsync` et accessible lorsque l’intergiciel est configuré :</span><span class="sxs-lookup"><span data-stu-id="b198b-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="b198b-115">Utiliser HttpContext à partir de composants personnalisés</span><span class="sxs-lookup"><span data-stu-id="b198b-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="b198b-116">Pour les autres infrastructures et les composants personnalisés qui requièrent l’accès à `HttpContext`, l’approche recommandée consiste à inscrire une dépendance à l’aide du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) intégré.</span><span class="sxs-lookup"><span data-stu-id="b198b-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="b198b-117">Le conteneur d’injection de dépendances fournit `IHttpContextAccessor` à toutes les classes qui le déclarent comme une dépendance dans leurs constructeurs :</span><span class="sxs-lookup"><span data-stu-id="b198b-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

<span data-ttu-id="b198b-118">Dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="b198b-118">In the following example:</span></span>

* <span data-ttu-id="b198b-119">`UserRepository` déclare sa dépendance par rapport à `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="b198b-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="b198b-120">Il y a dépendance lorsque l’instance de dépendances résout la chaîne de dépendance et crée une instance de `UserRepository`.</span><span class="sxs-lookup"><span data-stu-id="b198b-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="b198b-121">Accès de HttpContext à partir d’un thread en arrière-plan</span><span class="sxs-lookup"><span data-stu-id="b198b-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="b198b-122">`HttpContext` n’est pas thread-safe.</span><span class="sxs-lookup"><span data-stu-id="b198b-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="b198b-123">La lecture ou l’écriture de propriétés de `HttpContext` en dehors du traitement d’une requête peut entraîner une exception <xref:System.NullReferenceException>.</span><span class="sxs-lookup"><span data-stu-id="b198b-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="b198b-124">Si votre application génère des `NullReferenceException` Erreurs sporadiques, examinez les parties du code qui démarrent le traitement en arrière-plan ou qui poursuivent le traitement après la fin d’une demande.</span><span class="sxs-lookup"><span data-stu-id="b198b-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="b198b-125">Recherchez des erreurs, telles que la définition d’une méthode de contrôleur comme `async void` .</span><span class="sxs-lookup"><span data-stu-id="b198b-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="b198b-126">Pour effectuer un travail en arrière-plan en toute sécurité avec des données `HttpContext` :</span><span class="sxs-lookup"><span data-stu-id="b198b-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="b198b-127">Copiez les données nécessaires pendant le traitement de la requête.</span><span class="sxs-lookup"><span data-stu-id="b198b-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="b198b-128">Transmettez les données copiées à une tâche en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="b198b-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="b198b-129">Pour éviter tout code non sécurisé, ne transmettez jamais `HttpContext` à une méthode qui effectue un travail en arrière-plan.</span><span class="sxs-lookup"><span data-stu-id="b198b-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="b198b-130">Transmettez les données requises à la place.</span><span class="sxs-lookup"><span data-stu-id="b198b-130">Pass the required data instead.</span></span> <span data-ttu-id="b198b-131">Dans l’exemple suivant, `SendEmailCore` est appelé pour commencer à envoyer un message électronique.</span><span class="sxs-lookup"><span data-stu-id="b198b-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="b198b-132">`correlationId`Est passé à `SendEmailCore` , et non à `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="b198b-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="b198b-133">L’exécution du code n’attend pas la `SendEmailCore` fin de :</span><span class="sxs-lookup"><span data-stu-id="b198b-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
```

## <a name="no-locblazor-and-shared-state"></a><span data-ttu-id="b198b-134">Blazor et état partagé</span><span class="sxs-lookup"><span data-stu-id="b198b-134">Blazor and shared state</span></span>

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]
