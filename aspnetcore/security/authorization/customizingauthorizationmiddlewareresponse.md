---
title: Personnaliser le comportement de AuthorizationMiddleware
author: pranavkm
ms.author: prkrishn
description: Cet article explique comment personnaliser la gestion des résultats de AuthorizationMiddleware.
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156783"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="a441c-103">Personnaliser le comportement de AuthorizationMiddleware</span><span class="sxs-lookup"><span data-stu-id="a441c-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="a441c-104">Les applications peuvent inscrire un `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` pour personnaliser la façon dont l’intergiciel gère les résultats d’autorisation.</span><span class="sxs-lookup"><span data-stu-id="a441c-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="a441c-105">Les applications peuvent utiliser l’intergiciel (middleware) personnalisé pour :</span><span class="sxs-lookup"><span data-stu-id="a441c-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="a441c-106">Retourne des réponses personnalisées.</span><span class="sxs-lookup"><span data-stu-id="a441c-106">Return customized responses.</span></span>
* <span data-ttu-id="a441c-107">Améliorez la stimulation par défaut ou interdisez les réponses.</span><span class="sxs-lookup"><span data-stu-id="a441c-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="a441c-108">Le code suivant montre un exemple de gestionnaire d’autorisations qui retourne une réponse personnalisée pour certains types d’échecs d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="a441c-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="a441c-109">S’inscrire `MyAuthorizationMiddlewareResultHandler` dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a441c-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->