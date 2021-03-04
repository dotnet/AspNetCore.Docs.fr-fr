---
title: Exemples d’authentification pour ASP.NET Core
author: rick-anderson
description: Fournit des liens vers les exemples d’authentification dans le référentiel ASP.NET Core.
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110116"
---
# <a name="authentication-samples-for-aspnet-core"></a>Exemples d’authentification pour ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Le [référentiel ASP.net Core](https://github.com/dotnet/aspnetcore) contient les exemples d’authentification suivants ( `main` branche) :

* [Transformation de revendications](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* [Cookie identification](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)
* [Réponse d’échec d’autorisation personnalisée](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [Fournisseur de stratégie personnalisée-IAuthorizationPolicyProvider](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [Options et schémas d’authentification dynamique](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* [Revendications externes](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)
* [Sélection de cookie l’un ou l’autre schéma d’authentification en fonction de la demande](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [Restreint l’accès aux fichiers statiques](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a>Obtenir et exécuter les exemples

Les exemples de liens fournis dans cet article fournissent des exemples pour la prochaine version de ASP.NET Core. Pour obtenir un exemple pour la version actuelle ou une version antérieure, procédez comme suit :

* Sélectionnez la branche de version du [référentiel ASP.net Core](https://github.com/dotnet/aspnetcore)] ( https://github.com/dotnet/aspnetcore) . Par exemple, la `release/5.0` branche contient les exemples pour la version 5,0 de ASP.net core.
* Clonez ou téléchargez le référentiel ASP.NET Core.
* Sur votre système local, vérifiez que l’installation de la version de [Kit SDK .net Core](https://dotnet.microsoft.com/download/dotnet-core) correspond au clone du référentiel ASP.net core.
* Accédez à un exemple dans le `aspnetcore/src/Security/samples` dossier et exécutez l’exemple avec la [ `dotnet run` commande](/dotnet/core/tools/dotnet-run).
