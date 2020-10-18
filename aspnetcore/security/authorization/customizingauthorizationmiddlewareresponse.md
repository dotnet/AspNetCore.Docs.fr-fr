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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>Personnaliser le comportement de AuthorizationMiddleware

Les applications peuvent inscrire un `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` pour personnaliser la façon dont l’intergiciel gère les résultats d’autorisation. Les applications peuvent utiliser l’intergiciel (middleware) personnalisé pour :

* Retourne des réponses personnalisées.
* Améliorez la stimulation par défaut ou interdisez les réponses.

Le code suivant montre un exemple de gestionnaire d’autorisations qui retourne une réponse personnalisée pour certains types d’échecs d’autorisation :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

S’inscrire `MyAuthorizationMiddlewareResultHandler` dans `Startup.ConfigureServices` :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->