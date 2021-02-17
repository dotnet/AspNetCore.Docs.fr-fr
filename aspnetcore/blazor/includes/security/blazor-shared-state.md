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
ms.openlocfilehash: 5ff4e4368d9e6d7c8525ae4ef0625d176a256a85
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551519"
---
<span data-ttu-id="9bcc8-101">Blazor applications serveur en direct dans la mémoire du serveur.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-101">Blazor server apps live in server memory.</span></span> <span data-ttu-id="9bcc8-102">Cela signifie qu’il existe plusieurs applications hébergées dans le même processus.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-102">That means that there are multiple apps hosted within the same process.</span></span> <span data-ttu-id="9bcc8-103">Pour chaque session d’application, Blazor démarre un circuit avec sa propre étendue d’injection de conteneurs.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-103">For each app session, Blazor starts a circuit with its own DI container scope.</span></span> <span data-ttu-id="9bcc8-104">Cela signifie que les services délimités sont uniques par Blazor session.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-104">That means that scoped services are unique per Blazor session.</span></span>

> [!WARNING]
> <span data-ttu-id="9bcc8-105">Nous vous déconseillons d’utiliser les applications sur le même état de partage de serveur à l’aide des services Singleton, sauf si une extrême prudence est prise, car cela peut introduire des failles de sécurité, telles que la fuite de l’état utilisateur entre les circuits.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-105">We don't recommend apps on the same server share state using singleton services unless extreme care is taken, as this can introduce security vulnerabilities, such as leaking user state across circuits.</span></span>

<span data-ttu-id="9bcc8-106">Vous pouvez utiliser des services Singleton avec état dans Blazor les applications s’ils sont spécifiquement conçus pour celui-ci.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-106">You can use stateful singleton services in Blazor apps if they are specifically designed for it.</span></span> <span data-ttu-id="9bcc8-107">Par exemple, il est possible d’utiliser un cache mémoire en tant que singleton, car il requiert une clé pour accéder à une entrée donnée, en supposant que les utilisateurs n’ont pas le contrôle des clés de cache utilisées.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-107">For example, it's ok to use a memory cache as a singleton because it requires a key to access a given entry, assuming users don't have control of what cache keys are used.</span></span>

<span data-ttu-id="9bcc8-108">**De plus, pour des raisons de sécurité, vous ne devez pas utiliser <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> dans les Blazor applications.**</span><span class="sxs-lookup"><span data-stu-id="9bcc8-108">**Additionally, again for security reasons, you must not use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> within Blazor apps.**</span></span> <span data-ttu-id="9bcc8-109">Blazor les applications s’exécutent en dehors du contexte du pipeline ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-109">Blazor apps run outside of the context of the ASP.NET Core pipeline.</span></span> <span data-ttu-id="9bcc8-110">Le <xref:Microsoft.AspNetCore.Http.HttpContext> n’est pas forcément disponible dans le <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , et il n’est pas garanti qu’il détient le contexte qui a démarré l' Blazor application.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-110">The <xref:Microsoft.AspNetCore.Http.HttpContext> isn't guaranteed to be available within the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>, nor is it guaranteed to be holding the context that started the Blazor app.</span></span>

<span data-ttu-id="9bcc8-111">La méthode recommandée pour passer l’état de la demande à l' Blazor application consiste à utiliser des paramètres du composant racine dans le rendu initial de l’application :</span><span class="sxs-lookup"><span data-stu-id="9bcc8-111">The recommended way to pass request state to the Blazor app is through parameters to the root component in the initial rendering of the app:</span></span>

* <span data-ttu-id="9bcc8-112">Définissez une classe avec toutes les données que vous souhaitez transmettre à l' Blazor application.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-112">Define a class with all the data you want to pass to the Blazor app.</span></span>
* <span data-ttu-id="9bcc8-113">Renseignez ces données à partir de la Razor page en utilisant le <xref:Microsoft.AspNetCore.Http.HttpContext> disponible à ce moment-là.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-113">Populate that data from the Razor page using the <xref:Microsoft.AspNetCore.Http.HttpContext> available at that time.</span></span>
* <span data-ttu-id="9bcc8-114">Transmettez les données à l' Blazor application en tant que paramètre au composant racine (application).</span><span class="sxs-lookup"><span data-stu-id="9bcc8-114">Pass the data to the Blazor app as a parameter to the root component (App).</span></span>
* <span data-ttu-id="9bcc8-115">Définissez un paramètre dans le composant racine pour stocker les données passées à l’application.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-115">Define a parameter in the root component to hold the data being passed to the app.</span></span>
* <span data-ttu-id="9bcc8-116">Utilisez les données spécifiques à l’utilisateur dans l’application. vous pouvez également copier ces données dans un service étendu dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> afin qu’elles puissent être utilisées dans l’application.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-116">Use the user-specific data within the app; or alternatively, copy that data into a scoped service within <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> so that it can be used across the app.</span></span>

<span data-ttu-id="9bcc8-117">Pour obtenir des informations supplémentaires ainsi qu'un code d'exemple, consultez <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="9bcc8-117">For more information and example code, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>
