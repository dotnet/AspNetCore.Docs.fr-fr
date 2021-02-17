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
ms.openlocfilehash: 7caea4089d3624d4c02db4b8adbe9edb73f3d31a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551434"
---
> [!NOTE]
> <span data-ttu-id="ce64a-101">Avant ASP.NET Core 3,0, les applications Web écrivent un journal de type `LogLevel.Warning` par demande si la culture demandée n’est pas prise en charge.</span><span class="sxs-lookup"><span data-stu-id="ce64a-101">Prior to ASP.NET Core 3.0 web apps write one log of type `LogLevel.Warning` per request if the requested culture is unsupported.</span></span> <span data-ttu-id="ce64a-102">La journalisation `LogLevel.Warning` d’une par demande peut créer des fichiers journaux volumineux contenant des informations redondantes.</span><span class="sxs-lookup"><span data-stu-id="ce64a-102">Logging one `LogLevel.Warning` per request is can make large log files with redundant information.</span></span> <span data-ttu-id="ce64a-103">Ce comportement a été modifié dans ASP.NET 3,0.</span><span class="sxs-lookup"><span data-stu-id="ce64a-103">This behavior has been changed in ASP.NET 3.0.</span></span> <span data-ttu-id="ce64a-104">Le `RequestLocalizationMiddleware` écrit un journal de type `LogLevel.Debug` , ce qui réduit la taille des journaux de production.</span><span class="sxs-lookup"><span data-stu-id="ce64a-104">The `RequestLocalizationMiddleware` writes a log of type `LogLevel.Debug`, which reduces the size of production logs.</span></span>
