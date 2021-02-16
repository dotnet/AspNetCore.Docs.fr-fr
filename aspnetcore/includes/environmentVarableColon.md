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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536286"
---
Le `:` séparateur ne fonctionne pas avec les clés hiérarchiques de variable d’environnement sur toutes les plateformes. `__`, le double trait de soulignement, est :

* Pris en charge par toutes les plateformes. Par exemple, le `:` séparateur n’est pas pris en charge par [bash](https://linuxhint.com/bash-environment-variables/), mais `__` est.
* Automatiquement remplacé par un `:`