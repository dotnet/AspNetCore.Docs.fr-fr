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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552368"
---
## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="c7dfc-101">Partager du code d’interopérabilité dans une bibliothèque de classes</span><span class="sxs-lookup"><span data-stu-id="c7dfc-101">Share interop code in a class library</span></span>

<span data-ttu-id="c7dfc-102">Le code d’interopérabilité JS peut être inclus dans une bibliothèque de classes, ce qui vous permet de partager le code dans un package NuGet.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-102">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="c7dfc-103">La bibliothèque de classes gère l’incorporation des ressources JavaScript dans l’assembly généré.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-103">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="c7dfc-104">Les fichiers JavaScript sont placés dans le `wwwroot` dossier.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-104">The JavaScript files are placed in the `wwwroot` folder.</span></span> <span data-ttu-id="c7dfc-105">Les outils s’occupent de l’incorporation des ressources lors de la génération de la bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-105">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="c7dfc-106">Le package NuGet créé est référencé dans le fichier projet de l’application de la même façon que n’importe quel package NuGet.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-106">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="c7dfc-107">Une fois le package restauré, le code d’application peut appeler JavaScript comme s’il s’agissait de C#.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-107">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="c7dfc-108">Pour plus d’informations, consultez <xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="c7dfc-108">For more information, see <xref:blazor/components/class-libraries>.</span></span>
