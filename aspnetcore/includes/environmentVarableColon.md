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
<span data-ttu-id="ac359-101">Le `:` séparateur ne fonctionne pas avec les clés hiérarchiques de variable d’environnement sur toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="ac359-101">The `:` separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="ac359-102">`__`, le double trait de soulignement, est :</span><span class="sxs-lookup"><span data-stu-id="ac359-102">`__`, the double underscore, is:</span></span>

* <span data-ttu-id="ac359-103">Pris en charge par toutes les plateformes.</span><span class="sxs-lookup"><span data-stu-id="ac359-103">Supported by all platforms.</span></span> <span data-ttu-id="ac359-104">Par exemple, le `:` séparateur n’est pas pris en charge par [bash](https://linuxhint.com/bash-environment-variables/), mais `__` est.</span><span class="sxs-lookup"><span data-stu-id="ac359-104">For example, the `:` separator is not supported by [Bash](https://linuxhint.com/bash-environment-variables/), but `__` is.</span></span>
* <span data-ttu-id="ac359-105">Automatiquement remplacé par un `:`</span><span class="sxs-lookup"><span data-stu-id="ac359-105">Automatically replaced by a `:`</span></span>