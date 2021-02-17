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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552006"
---
<span data-ttu-id="73a9a-101">L’intergiciel (middleware) d’en-têtes transférés doit s’exécuter avant tout autre intergiciel.</span><span class="sxs-lookup"><span data-stu-id="73a9a-101">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="73a9a-102">Cet ordre permet au middleware qui repose sur les informations des en-têtes transférés d’utiliser les valeurs d’en-tête pour le traitement.</span><span class="sxs-lookup"><span data-stu-id="73a9a-102">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="73a9a-103">Pour exécuter l’intergiciel (middleware) d’en-têtes transférés après les diagnostics et l’intergiciel (middleware) de gestion des erreurs, consultez [ordre des intergiciels en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#fhmo).</span><span class="sxs-lookup"><span data-stu-id="73a9a-103">To run Forwarded Headers Middleware after diagnostics and error handling middleware, see [Forwarded Headers Middleware order](xref:host-and-deploy/proxy-load-balancer#fhmo).</span></span>