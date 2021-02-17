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
L’intergiciel (middleware) d’en-têtes transférés doit s’exécuter avant tout autre intergiciel. Cet ordre permet au middleware qui repose sur les informations des en-têtes transférés d’utiliser les valeurs d’en-tête pour le traitement. Pour exécuter l’intergiciel (middleware) d’en-têtes transférés après les diagnostics et l’intergiciel (middleware) de gestion des erreurs, consultez [ordre des intergiciels en-têtes transférés](xref:host-and-deploy/proxy-load-balancer#fhmo).