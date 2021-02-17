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
<span data-ttu-id="3b8b5-101">Les codes de fin HTTP sont similaires aux en-têtes HTTP, sauf qu’ils sont envoyés après l’envoi du corps de la réponse.</span><span class="sxs-lookup"><span data-stu-id="3b8b5-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="3b8b5-102">Pour IIS et HTTP.sys, seules les codes de fin de réponse HTTP/2 sont pris en charge.</span><span class="sxs-lookup"><span data-stu-id="3b8b5-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="3b8b5-103">Dans l’exemple de code précédent :</span><span class="sxs-lookup"><span data-stu-id="3b8b5-103">In the preceding example code:</span></span>

* <span data-ttu-id="3b8b5-104">`SupportsTrailers` garantit que les codes de fin sont pris en charge pour la réponse.</span><span class="sxs-lookup"><span data-stu-id="3b8b5-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="3b8b5-105">`DeclareTrailer` Ajoute le nom de la terminaison donnée à l' `Trailer` en-tête de réponse.</span><span class="sxs-lookup"><span data-stu-id="3b8b5-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="3b8b5-106">La déclaration des codes de fin d’une réponse est facultative, mais recommandée.</span><span class="sxs-lookup"><span data-stu-id="3b8b5-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="3b8b5-107">Si `DeclareTrailer` est appelé, il doit être avant l’envoi des en-têtes de réponse.</span><span class="sxs-lookup"><span data-stu-id="3b8b5-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="3b8b5-108">`AppendTrailer` Ajoute le code de fin.</span><span class="sxs-lookup"><span data-stu-id="3b8b5-108">`AppendTrailer` appends the trailer.</span></span>
