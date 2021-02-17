---
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
ms.openlocfilehash: b6f6bc2e094c9070e0ea57b507f558313f19bc15
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552122"
---
ASP.NET Core [Identity](xref:security/authentication/identity) n’est en grande partie pas affectée par [ cookie SameSite](xref:security/samesite) , à l’exception des scénarios avancés tels que `IFrames` ou l' `OpenIdConnect` intégration.

Lorsque vous utilisez `Identity` ,  n’ajoutez pas cookie de fournisseurs ou ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` d’appels, `Identity` ce qui est nécessaire.