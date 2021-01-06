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
# <a name="access-httpcontext-in-aspnet-core"></a>Accéder à HttpContext dans ASP.NET Core

ASP.NET Core les applications accessibles par `HttpContext` le biais de l' <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface et de son implémentation par défaut <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> . Il est seulement nécessaire d’utiliser `IHttpContextAccessor` quand vous avez besoin d’accéder à `HttpContext` à l’intérieur d’un service.

## <a name="use-httpcontext-from-no-locrazor-pages"></a>Utiliser HttpContext à partir de Razor pages

Les Razor pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposent la <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> propriété :

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

## <a name="use-httpcontext-from-a-no-locrazor-view"></a>Utiliser HttpContext à partir d’une Razor vue

Razorles vues exposent `HttpContext` directement le via une propriété [ Razor page. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) sur la vue. L’exemple suivant récupère le nom d’utilisateur actuel dans une application intranet à l’aide de l’authentification Windows :

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Utiliser HttpContext à partir d’un contrôleur

Les contrôleurs exposent la propriété [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) :

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

## <a name="use-httpcontext-from-middleware"></a>Utiliser HttpContext à partir d’un intergiciel

Lorsque vous travaillez avec des composants d’intergiciel personnalisés, `HttpContext` est transmis à la méthode `Invoke` ou `InvokeAsync` et accessible lorsque l’intergiciel est configuré :

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Utiliser HttpContext à partir de composants personnalisés

Pour les autres infrastructures et les composants personnalisés qui requièrent l’accès à `HttpContext`, l’approche recommandée consiste à inscrire une dépendance à l’aide du conteneur d’[injection de dépendances](xref:fundamentals/dependency-injection) intégré. Le conteneur d’injection de dépendances fournit `IHttpContextAccessor` à toutes les classes qui le déclarent comme une dépendance dans leurs constructeurs :

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

Dans l’exemple suivant :

* `UserRepository` déclare sa dépendance par rapport à `IHttpContextAccessor`.
* Il y a dépendance lorsque l’instance de dépendances résout la chaîne de dépendance et crée une instance de `UserRepository`.

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

## <a name="httpcontext-access-from-a-background-thread"></a>Accès de HttpContext à partir d’un thread en arrière-plan

`HttpContext` n’est pas thread-safe. La lecture ou l’écriture de propriétés de `HttpContext` en dehors du traitement d’une requête peut entraîner une exception <xref:System.NullReferenceException>.

> [!NOTE]
> Si votre application génère des `NullReferenceException` Erreurs sporadiques, examinez les parties du code qui démarrent le traitement en arrière-plan ou qui poursuivent le traitement après la fin d’une demande. Recherchez des erreurs, telles que la définition d’une méthode de contrôleur comme `async void` .

Pour effectuer un travail en arrière-plan en toute sécurité avec des données `HttpContext` :

* Copiez les données nécessaires pendant le traitement de la requête.
* Transmettez les données copiées à une tâche en arrière-plan.

Pour éviter tout code non sécurisé, ne transmettez jamais `HttpContext` à une méthode qui effectue un travail en arrière-plan. Transmettez les données requises à la place. Dans l’exemple suivant, `SendEmailCore` est appelé pour commencer à envoyer un message électronique. `correlationId`Est passé à `SendEmailCore` , et non à `HttpContext` . L’exécution du code n’attend pas la `SendEmailCore` fin de :

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

## <a name="no-locblazor-and-shared-state"></a>Blazor et état partagé

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]
