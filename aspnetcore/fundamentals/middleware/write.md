---
title: Écrire un intergiciel (middleware) ASP.NET Core personnalisé
author: rick-anderson
description: Découvrez comment écrire un intergiciel (middleware) ASP.NET Core personnalisé.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
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
uid: fundamentals/middleware/write
ms.openlocfilehash: 5f33691cbcc00f407fff907ca62547fd80f2aa3c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057464"
---
# <a name="write-custom-aspnet-core-middleware"></a>Écrire un intergiciel (middleware) ASP.NET Core personnalisé

De [Rick Anderson](https://twitter.com/RickAndMSFT) et [Steve Smith](https://ardalis.com/)

Un middleware est un logiciel qui est assemblé dans un pipeline d’application pour gérer les requêtes et les réponses. Même si ASP.NET Core propose une large palette de composants d’intergiciel (middleware) intégrés, dans certains scénarios, vous voudrez certainement écrire un intergiciel personnalisé.

> [!NOTE]
> Cette rubrique explique comment écrire *un intergiciel (middleware) basé sur des conventions* . Pour une approche qui utilise le typage fort et l’activation par demande, consultez <xref:fundamentals/middleware/extensibility> .

## <a name="middleware-class"></a>Classe d’intergiciel (middleware)

Les intergiciels sont généralement encapsulés dans une classe et exposés avec une méthode d’extension. Prenez en compte le middleware suivant, qui définit la culture de la requête actuelle à partir d’une chaîne de requête :

[!code-csharp[](write/snapshot/StartupCulture.cs)]

L’exemple de code précédent est utilisé pour illustrer la création d’un composant de middleware. Pour plus d’informations sur la prise en charge intégrée de la localisation par ASP.NET Core, consultez <xref:fundamentals/localization>.

Testez l’intergiciel en transmettant la culture. Par exemple, demandez `https://localhost:5001/?culture=no`.

Le code suivant déplace le délégué de l’intergiciel dans une classe :

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

La classe d’intergiciel (middleware) doit inclure :

* Un constructeur public avec un paramètre de type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.
* Une méthode publique nommée `Invoke` ou `InvokeAsync`. Cette méthode doit :
  * Retournez une `Task`.
  * Accepter un premier paramètre de type <xref:Microsoft.AspNetCore.Http.HttpContext>.
  
Les paramètres supplémentaires pour le constructeur et `Invoke`/`InvokeAsync` sont remplis par [Injection de dépendance (DI)](xref:fundamentals/dependency-injection).

## <a name="middleware-dependencies"></a>Dépendances de l’intergiciel (middleware)

L’intergiciel doit suivre le [principe de dépendances explicites](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) en exposant ses dépendances dans son constructeur. L’intergiciel est construit une fois par *durée de vie d’application*. Consultez la section [Dépendances des middlewares par requête](#per-request-middleware-dependencies) si vous avez besoin de partager des services avec un middleware au sein d’une requête.

Les composants de middleware peuvent résoudre leurs dépendances à partir de l’[injection de dépendances](xref:fundamentals/dependency-injection) à l’aide des paramètres du constructeur. [UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) peut également accepter des paramètres supplémentaires directement.

## <a name="per-request-middleware-dependencies"></a>Dépendances de l’intergiciel (middleware) par requête

Étant donné que le middleware est construit au démarrage de l’application, et non par requête, les services de durée de vie *délimités* utilisés par les constructeurs du middleware ne sont pas partagés avec d’autres types injectés par des dépendances lors de chaque requête. Si vous devez partager un service *délimité* entre votre intergiciel et d’autres types, ajoutez-le à la signature de la méthode `Invoke`. La méthode `Invoke` peut accepter des paramètres supplémentaires qui sont renseignés par injection de dépendances :

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

[Les options de durée de vie et d’inscription](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contiennent un exemple complet d’intergiciel (middleware) avec des services de durée de vie *délimités* .

## <a name="middleware-extension-method"></a>Méthode d’extension d’intergiciel

La méthode d’extension suivante expose le middleware par le biais de <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> :

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

Le code suivant appelle l’intergiciel à partir de `Startup.Configure` :

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Ressources supplémentaires

* Les [options de durée de vie et d’inscription](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contiennent un exemple complet d’intergiciel (middleware) avec des services de durée de vie *délimités*, *transitoires* et *singletons* .
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
