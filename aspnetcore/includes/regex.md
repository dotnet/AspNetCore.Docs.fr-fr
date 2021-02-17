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
ms.openlocfilehash: 73395ab632f38fef1348b4657770eb52db5778d9
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552294"
---
> [!WARNING]
> <span data-ttu-id="73234-101">Lorsque <xref:System.Text.RegularExpressions> vous utilisez pour traiter une entrée non fiable, transmettez un délai d’attente.</span><span class="sxs-lookup"><span data-stu-id="73234-101">When using <xref:System.Text.RegularExpressions> to process untrusted input, pass a timeout.</span></span> <span data-ttu-id="73234-102">Un utilisateur malveillant peut fournir une entrée pour `RegularExpressions` provoquer une [attaque par déni de service](https://www.us-cert.gov/ncas/tips/ST04-015).</span><span class="sxs-lookup"><span data-stu-id="73234-102">A malicious user can provide input to `RegularExpressions` causing a [Denial-of-Service attack](https://www.us-cert.gov/ncas/tips/ST04-015).</span></span> <span data-ttu-id="73234-103">ASP.NET Core les API d’infrastructure qui utilisent `RegularExpressions` passent un délai d’expiration.</span><span class="sxs-lookup"><span data-stu-id="73234-103">ASP.NET Core framework APIs that use `RegularExpressions` pass a timeout.</span></span>