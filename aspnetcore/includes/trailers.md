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
ms.openlocfilehash: d5e1630d6a9e412c40831aa3a66aaed7524ff501
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552297"
---
Les codes de fin HTTP sont similaires aux en-têtes HTTP, sauf qu’ils sont envoyés après l’envoi du corps de la réponse. Pour IIS et HTTP.sys, seules les codes de fin de réponse HTTP/2 sont pris en charge.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

Dans l’exemple de code précédent :

* `SupportsTrailers` garantit que les codes de fin sont pris en charge pour la réponse.
* `DeclareTrailer` Ajoute le nom de la terminaison donnée à l' `Trailer` en-tête de réponse. La déclaration des codes de fin d’une réponse est facultative, mais recommandée. Si `DeclareTrailer` est appelé, il doit être avant l’envoi des en-têtes de réponse.
* `AppendTrailer` Ajoute le code de fin.
