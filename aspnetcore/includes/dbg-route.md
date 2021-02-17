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
ms.openlocfilehash: 087c5a7dd20bc653a05c337dde905716032e00b3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552072"
---
## <a name="debug-diagnostics"></a><span data-ttu-id="7deb8-101">Diagnostics de débogage</span><span class="sxs-lookup"><span data-stu-id="7deb8-101">Debug diagnostics</span></span>

<span data-ttu-id="7deb8-102">Pour une sortie de diagnostic de routage détaillée, affectez à la valeur `Logging:LogLevel:Microsoft` `Debug` .</span><span class="sxs-lookup"><span data-stu-id="7deb8-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="7deb8-103">Dans l’environnement de développement, définissez le niveau de journalisation dans *appsettings.Development.jssur*:</span><span class="sxs-lookup"><span data-stu-id="7deb8-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
