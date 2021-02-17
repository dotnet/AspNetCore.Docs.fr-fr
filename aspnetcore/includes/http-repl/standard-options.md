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
ms.openlocfilehash: 30c4c469453f0202fe5310dbe00b3d7214f49b5a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551463"
---
* `-F|--no-formatting`

  Indicateur dont la présence provoque la suppression de la mise en forme de la réponse HTTP.

* `-h|--header`

  Définit un en-tête de requête HTTP. Les deux formats de fichier suivants sont pris en charge :

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  Spécifie un fichier dans lequel le corps de la réponse HTTP doit être écrit. Par exemple : `--response:body "C:\response.json"`. Le fichier est créé s’il n’existe pas.

* `--response:headers`

  Spécifie un fichier dans lequel les en-têtes de la réponse HTTP doivent être écrits. Par exemple : `--response:headers "C:\response.txt"`. Le fichier est créé s’il n’existe pas.

* `-s|--streaming`

  Indicateur dont la présence provoque l’activation du streaming de la réponse HTTP.
