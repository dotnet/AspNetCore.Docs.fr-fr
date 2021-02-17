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
ms.openlocfilehash: 164c9e8f73502fc627c4514bd683dc183d318b1d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551740"
---
La réinitialisation permet au serveur de réinitialiser une requête HTTP/2 avec un code d’erreur spécifié. Une demande de réinitialisation est considérée comme abandonnée.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` dans l’exemple de code précédent, spécifie le `INTERNAL_ERROR` code d’erreur. Pour plus d’informations sur les codes d’erreur HTTP/2, consultez la [section Code d’erreur de la spécification http/2](https://tools.ietf.org/html/rfc7540#page-50).