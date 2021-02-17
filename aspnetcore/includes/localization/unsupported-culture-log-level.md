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
> Avant ASP.NET Core 3,0, les applications Web écrivent un journal de type `LogLevel.Warning` par demande si la culture demandée n’est pas prise en charge. La journalisation `LogLevel.Warning` d’une par demande peut créer des fichiers journaux volumineux contenant des informations redondantes. Ce comportement a été modifié dans ASP.NET 3,0. Le `RequestLocalizationMiddleware` écrit un journal de type `LogLevel.Debug` , ce qui réduit la taille des journaux de production.
