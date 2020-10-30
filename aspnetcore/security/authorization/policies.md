---
title: Autorisation basée sur la stratégie dans ASP.NET Core
author: rick-anderson
description: Découvrez comment créer et utiliser des gestionnaires de stratégie d’autorisation pour appliquer les exigences d’autorisation dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
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
uid: security/authorization/policies
ms.openlocfilehash: 286dc3bcc66b86a2a6b7d3cb7b6052bf7b474aff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060207"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Autorisation basée sur la stratégie dans ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

En coulisses, l’autorisation basée [sur les rôles](xref:security/authorization/roles) et l' [autorisation basée sur les revendications](xref:security/authorization/claims) utilisent une spécification, un gestionnaire de spécification et une stratégie préconfigurée. Ces blocs de construction prennent en charge l’expression des évaluations d’autorisation dans le code. Le résultat est une structure d’autorisation plus riche, réutilisable et testable.

Une stratégie d’autorisation se compose d’une ou de plusieurs exigences. Elle est inscrite dans le cadre de la configuration du service d’autorisation, dans la `Startup.ConfigureServices` méthode :

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

Dans l’exemple précédent, une stratégie « AtLeast21 » est créée. Il a une exigence unique &mdash; qui correspond à une ancienneté minimale, qui est fournie en tant que paramètre à la spécification.

## <a name="iauthorizationservice"></a>IAuthorizationService

Le service principal qui détermine si l’autorisation est réussie est le <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> suivant :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Le code précédent met en évidence les deux méthodes de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> est un service de marqueur sans méthode, et le mécanisme de suivi de la réussite de l’autorisation.

Chaque <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de la vérification de la satisfaction des exigences suivantes :
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les spécifications ont été satisfaites :

```csharp
 context.Succeed(requirement)
```

Le code suivant illustre l’implémentation par défaut simplifiée (et annotée avec des commentaires) du service d’autorisation :

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

Le code suivant illustre un exemple typique `ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

Utilisez <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` pour l’autorisation.

## <a name="apply-policies-to-mvc-controllers"></a>Appliquer des stratégies à des contrôleurs MVC

Si vous utilisez des Razor pages, consultez [appliquer des stratégies à des Razor pages](#apply-policies-to-razor-pages) de ce document.

Les stratégies sont appliquées aux contrôleurs à l’aide de l' `[Authorize]` attribut avec le nom de la stratégie. Exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>Appliquer des stratégies à des Razor pages

Les stratégies sont appliquées aux pages à l' Razor aide de l' `[Authorize]` attribut avec le nom de la stratégie. Exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Les stratégies ne peuvent **pas** être appliquées au Razor niveau du gestionnaire de page, elles doivent être appliquées à la page.

Les stratégies peuvent être appliquées à Razor des pages à l’aide d’une [Convention d’autorisation](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Spécifications

Une spécification d’autorisation est une collection de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal d’utilisateur actuel. Dans notre stratégie « AtLeast21 », l’exigence est un paramètre unique de &mdash; l’âge minimal. Une spécification implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide. Une exigence d’ancienneté minimale paramétrable peut être implémentée comme suit :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Si une stratégie d’autorisation contient plusieurs exigences d’autorisation, toutes les spécifications doivent réussir pour que l’évaluation de la stratégie aboutisse. En d’autres termes, plusieurs exigences d’autorisation ajoutées à une seule stratégie d’autorisation sont traitées sur une base _ *et* *.

> [!NOTE]
> Une exigence n’a pas besoin d’avoir des données ou des propriétés.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Gestionnaires d’autorisations

Un gestionnaire d’autorisations est responsable de l’évaluation des propriétés d’une spécification. Le gestionnaire d’autorisations évalue les spécifications par rapport à un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.

Une spécification peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers). Un gestionnaire peut hériter de [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est la spécification à gérer. Un gestionnaire peut également implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plusieurs types d’exigences.

### <a name="use-a-handler-for-one-requirement"></a>Utiliser un gestionnaire pour une spécification

<a name="security-authorization-handler-example"></a>

Voici un exemple de relation un-à-un dans laquelle un gestionnaire d’âge minimal utilise une seule exigence :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Le code précédent détermine si le principal de l’utilisateur actuel possède une revendication de date de naissance qui a été émise par un émetteur connu et approuvé. L’autorisation ne peut pas se produire lorsque la revendication est manquante, auquel cas une tâche terminée est retournée. Lorsqu’une revendication est présente, l’âge de l’utilisateur est calculé. Si l’utilisateur atteint l’ancienneté minimale définie par la spécification, l’autorisation est considérée comme réussie. Lorsque l’autorisation est réussie, `context.Succeed` est appelée avec la spécification satisfaite comme paramètre unique.

### <a name="use-a-handler-for-multiple-requirements"></a>Utiliser un gestionnaire pour plusieurs spécifications

Voici un exemple de relation un-à-plusieurs dans laquelle un gestionnaire d’autorisations peut gérer trois types différents d’exigences :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Le code précédent parcourt [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; une propriété contenant des exigences non marquées comme ayant réussi. Pour une `ReadPermission` spécification, l’utilisateur doit être un propriétaire ou un sponsor pour accéder à la ressource demandée. Dans le cas d’une `EditPermission` `DeletePermission` exigence ou, il doit être propriétaire de l’accès à la ressource demandée.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Inscription du gestionnaire

Les gestionnaires sont inscrits dans la collection de services pendant la configuration. Exemple :

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Le code précédent s’inscrit `MinimumAgeHandler` en tant que singleton en appelant `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . Les gestionnaires peuvent être inscrits à l’aide de l’une des [durées de vie de service](xref:fundamentals/dependency-injection#service-lifetimes)intégrées.

## <a name="what-should-a-handler-return"></a>Que doit retourner un gestionnaire ?

Notez que la `Handle` méthode dans l' [exemple de gestionnaire](#security-authorization-handler-example) ne retourne aucune valeur. Comment l’état de la réussite ou de l’échec est-il indiqué ?

* Un gestionnaire indique la réussite en appelant `context.Succeed(IAuthorizationRequirement requirement)` , en passant la spécification qui a été validée avec succès.

* Un gestionnaire n’A pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même spécification peuvent être exécutés correctement.

* Pour garantir la défaillance, même si d’autres gestionnaires de spécifications sont correctement exécutés, appelez `context.Fail` .

Si un gestionnaire appelle `context.Succeed` ou `context.Fail` , tous les autres gestionnaires sont toujours appelés. Cela permet aux exigences de produire des effets secondaires, tels que la journalisation, qui se produit même si un autre gestionnaire a validé ou échoué avec succès. Lorsque la valeur `false` est définie sur, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) les courts-circuits de l’exécution des gestionnaires lorsque `context.Fail` est appelé. `InvokeHandlersAfterFailure` la valeur par défaut `true` est, auquel cas tous les gestionnaires sont appelés.

> [!NOTE]
> Les gestionnaires d’autorisations sont appelés même si l’authentification échoue.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Pourquoi dois-je utiliser plusieurs gestionnaires pour une spécification ?

Dans les cas où vous souhaitez que l’évaluation soit sur une base **ou** , implémentez plusieurs gestionnaires pour une seule exigence. Par exemple, Microsoft possède des portes qui s’ouvrent uniquement avec des cartes clés. Si vous laissez votre carte clé chez vous, la réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous. Dans ce scénario, vous auriez besoin d’une seule exigence, *BuildingEntry* , mais plusieurs gestionnaires, chacun examinant une seule exigence.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Vérifiez que les deux gestionnaires sont [inscrits](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Si l’un des gestionnaires aboutit lorsqu’une stratégie évalue le `BuildingEntryRequirement` , l’évaluation de la stratégie aboutit.

## <a name="use-a-func-to-fulfill-a-policy"></a>Utiliser un Func pour accomplir une stratégie

Il peut arriver que la réalisation d’une stratégie soit simple à exprimer dans le code. Il est possible de fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie avec le `RequireAssertion` Générateur de stratégie.

Par exemple, la précédente `BadgeEntryHandler` peut être réécrite comme suit :

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a>Accéder au contexte de requête MVC dans les gestionnaires

La `HandleRequirementAsync` méthode que vous implémentez dans un gestionnaire d’autorisations a deux paramètres : un `AuthorizationHandlerContext` et le `TRequirement` que vous gérez. Les infrastructures telles que MVC ou SignalR sont libres d’ajouter n’importe quel objet à la `Resource` propriété sur le `AuthorizationHandlerContext` pour passer des informations supplémentaires.

Lorsque vous utilisez le routage de point de terminaison, l’autorisation est généralement gérée par l’intergiciel (middleware) d’autorisation. Dans ce cas, la `Resource` propriété est une instance de <xref:Microsoft.AspNetCore.Http.Endpoint> . Le point de terminaison peut être utilisé pour sonder la ressource sous-jacente vers laquelle vous effectuez le routage. Exemple :

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Le point de terminaison ne fournit pas l’accès à l’objet actuel `HttpContext` . Lorsque vous utilisez le routage de point de terminaison, utilisez `IHttpContextAcessor` pour accéder à `HttpContext` l’intérieur d’un gestionnaire d’autorisations. Pour plus d’informations, consultez [utiliser HttpContext à partir de composants personnalisés](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).

Avec le routage traditionnel, ou lorsque l’autorisation a lieu dans le cadre du filtre d’autorisation MVC, la valeur de `Resource` est une <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance. Cette propriété fournit l’accès à `HttpContext` , `RouteData` et tout le reste fourni par MVC et les Razor pages.

L’utilisation de la `Resource` propriété est spécifique à l’infrastructure. L’utilisation des informations de la `Resource` propriété limite vos stratégies d’autorisation à des infrastructures spécifiques. Vous devez effectuer un cast de la `Resource` propriété à l’aide du `is` mot clé, puis confirmer que le cast a réussi pour s’assurer que votre code ne se bloque pas avec un lorsqu’il est `InvalidCastException` exécuté sur d’autres infrastructures :

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Exiger que tous les utilisateurs soient authentifiés globalement

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

En coulisses, l’autorisation basée [sur les rôles](xref:security/authorization/roles) et l' [autorisation basée sur les revendications](xref:security/authorization/claims) utilisent une spécification, un gestionnaire de spécification et une stratégie préconfigurée. Ces blocs de construction prennent en charge l’expression des évaluations d’autorisation dans le code. Le résultat est une structure d’autorisation plus riche, réutilisable et testable.

Une stratégie d’autorisation se compose d’une ou de plusieurs exigences. Elle est inscrite dans le cadre de la configuration du service d’autorisation, dans la `Startup.ConfigureServices` méthode :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

Dans l’exemple précédent, une stratégie « AtLeast21 » est créée. Il a une exigence unique &mdash; qui correspond à une ancienneté minimale, qui est fournie en tant que paramètre à la spécification.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Le service principal qui détermine si l’autorisation est réussie est le <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> suivant :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

Le code précédent met en évidence les deux méthodes de [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> est un service de marqueur sans méthode, et le mécanisme de suivi de la réussite de l’autorisation.

Chaque <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> est responsable de la vérification de la satisfaction des exigences suivantes :
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

La <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> classe est ce que le gestionnaire utilise pour marquer si les spécifications ont été satisfaites :

```csharp
 context.Succeed(requirement)
```

Le code suivant illustre l’implémentation par défaut simplifiée (et annotée avec des commentaires) du service d’autorisation :

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

Le code suivant illustre un exemple typique `ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

Utilisez <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> ou `[Authorize(Policy = "Something")]` pour l’autorisation.

## <a name="apply-policies-to-mvc-controllers"></a>Appliquer des stratégies à des contrôleurs MVC

Si vous utilisez des Razor pages, consultez [appliquer des stratégies à des Razor pages](#apply-policies-to-razor-pages) de ce document.

Les stratégies sont appliquées aux contrôleurs à l’aide de l' `[Authorize]` attribut avec le nom de la stratégie. Exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>Appliquer des stratégies à des Razor pages

Les stratégies sont appliquées aux pages à l' Razor aide de l' `[Authorize]` attribut avec le nom de la stratégie. Exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Les stratégies peuvent également être appliquées aux Razor pages à l’aide d’une [Convention d’autorisation](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Spécifications

Une spécification d’autorisation est une collection de paramètres de données qu’une stratégie peut utiliser pour évaluer le principal d’utilisateur actuel. Dans notre stratégie « AtLeast21 », l’exigence est un paramètre unique de &mdash; l’âge minimal. Une spécification implémente [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), qui est une interface de marqueur vide. Une exigence d’ancienneté minimale paramétrable peut être implémentée comme suit :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Si une stratégie d’autorisation contient plusieurs exigences d’autorisation, toutes les spécifications doivent réussir pour que l’évaluation de la stratégie aboutisse. En d’autres termes, plusieurs exigences d’autorisation ajoutées à une même stratégie d’autorisation sont traitées sur une base **et** .

> [!NOTE]
> Une exigence n’a pas besoin d’avoir des données ou des propriétés.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Gestionnaires d’autorisations

Un gestionnaire d’autorisations est responsable de l’évaluation des propriétés d’une spécification. Le gestionnaire d’autorisations évalue les spécifications par rapport à un [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) fourni pour déterminer si l’accès est autorisé.

Une spécification peut avoir [plusieurs gestionnaires](#security-authorization-policies-based-multiple-handlers). Un gestionnaire peut hériter de [AuthorizationHandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), où `TRequirement` est la spécification à gérer. Un gestionnaire peut également implémenter [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) pour gérer plusieurs types d’exigences.

### <a name="use-a-handler-for-one-requirement"></a>Utiliser un gestionnaire pour une spécification

<a name="security-authorization-handler-example"></a>

Voici un exemple de relation un-à-un dans laquelle un gestionnaire d’âge minimal utilise une seule exigence :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Le code précédent détermine si le principal de l’utilisateur actuel possède une revendication de date de naissance qui a été émise par un émetteur connu et approuvé. L’autorisation ne peut pas se produire lorsque la revendication est manquante, auquel cas une tâche terminée est retournée. Lorsqu’une revendication est présente, l’âge de l’utilisateur est calculé. Si l’utilisateur atteint l’ancienneté minimale définie par la spécification, l’autorisation est considérée comme réussie. Lorsque l’autorisation est réussie, `context.Succeed` est appelée avec la spécification satisfaite comme paramètre unique.

### <a name="use-a-handler-for-multiple-requirements"></a>Utiliser un gestionnaire pour plusieurs spécifications

Voici un exemple de relation un-à-plusieurs dans laquelle un gestionnaire d’autorisations peut gérer trois types différents d’exigences :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Le code précédent parcourt [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) &mdash; une propriété contenant des exigences non marquées comme ayant réussi. Pour une `ReadPermission` spécification, l’utilisateur doit être un propriétaire ou un sponsor pour accéder à la ressource demandée. Dans le cas d’une `EditPermission` `DeletePermission` exigence ou, il doit être propriétaire de l’accès à la ressource demandée.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Inscription du gestionnaire

Les gestionnaires sont inscrits dans la collection de services pendant la configuration. Exemple :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Le code précédent s’inscrit `MinimumAgeHandler` en tant que singleton en appelant `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . Les gestionnaires peuvent être inscrits à l’aide de l’une des [durées de vie de service](xref:fundamentals/dependency-injection#service-lifetimes)intégrées.

## <a name="what-should-a-handler-return"></a>Que doit retourner un gestionnaire ?

Notez que la `Handle` méthode dans l' [exemple de gestionnaire](#security-authorization-handler-example) ne retourne aucune valeur. Comment l’état de la réussite ou de l’échec est-il indiqué ?

* Un gestionnaire indique la réussite en appelant `context.Succeed(IAuthorizationRequirement requirement)` , en passant la spécification qui a été validée avec succès.

* Un gestionnaire n’A pas besoin de gérer les défaillances en général, car d’autres gestionnaires pour la même spécification peuvent être exécutés correctement.

* Pour garantir la défaillance, même si d’autres gestionnaires de spécifications sont correctement exécutés, appelez `context.Fail` .

Si un gestionnaire appelle `context.Succeed` ou `context.Fail` , tous les autres gestionnaires sont toujours appelés. Cela permet aux exigences de produire des effets secondaires, tels que la journalisation, qui se produit même si un autre gestionnaire a validé ou échoué avec succès. Lorsque la valeur `false` est définie sur, la propriété [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) les courts-circuits de l’exécution des gestionnaires lorsque `context.Fail` est appelé. `InvokeHandlersAfterFailure` la valeur par défaut `true` est, auquel cas tous les gestionnaires sont appelés.

> [!NOTE]
> Les gestionnaires d’autorisations sont appelés même si l’authentification échoue.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Pourquoi dois-je utiliser plusieurs gestionnaires pour une spécification ?

Dans les cas où vous souhaitez que l’évaluation soit sur une base **ou** , implémentez plusieurs gestionnaires pour une seule exigence. Par exemple, Microsoft possède des portes qui s’ouvrent uniquement avec des cartes clés. Si vous laissez votre carte clé chez vous, la réceptionniste imprime un autocollant temporaire et ouvre la porte pour vous. Dans ce scénario, vous auriez besoin d’une seule exigence, *BuildingEntry* , mais plusieurs gestionnaires, chacun examinant une seule exigence.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Vérifiez que les deux gestionnaires sont [inscrits](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Si l’un des gestionnaires aboutit lorsqu’une stratégie évalue le `BuildingEntryRequirement` , l’évaluation de la stratégie aboutit.

## <a name="use-a-func-to-fulfill-a-policy"></a>Utiliser un Func pour accomplir une stratégie

Il peut arriver que la réalisation d’une stratégie soit simple à exprimer dans le code. Il est possible de fournir un `Func<AuthorizationHandlerContext, bool>` lors de la configuration de votre stratégie avec le `RequireAssertion` Générateur de stratégie.

Par exemple, la précédente `BadgeEntryHandler` peut être réécrite comme suit :

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a>Accéder au contexte de requête MVC dans les gestionnaires

La `HandleRequirementAsync` méthode que vous implémentez dans un gestionnaire d’autorisations a deux paramètres : un `AuthorizationHandlerContext` et le `TRequirement` que vous gérez. Les infrastructures telles que MVC ou SignalR sont libres d’ajouter n’importe quel objet à la `Resource` propriété sur le `AuthorizationHandlerContext` pour passer des informations supplémentaires.

Par exemple, MVC passe une instance de [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) dans la `Resource` propriété. Cette propriété fournit l’accès à `HttpContext` , `RouteData` et tout le reste fourni par MVC et les Razor pages.

L’utilisation de la `Resource` propriété est spécifique à l’infrastructure. L’utilisation des informations de la `Resource` propriété limite vos stratégies d’autorisation à des infrastructures spécifiques. Vous devez effectuer un cast de la `Resource` propriété à l’aide du `is` mot clé, puis confirmer que le cast a réussi pour s’assurer que votre code ne se bloque pas avec un lorsqu’il est `InvalidCastException` exécuté sur d’autres infrastructures :

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
