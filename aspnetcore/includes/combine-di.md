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
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536287"
---
<a name="csc"></a>

<span data-ttu-id="e250c-101">Considérez la `ConfigureServices` méthode suivante, qui inscrit les services et configure les options :</span><span class="sxs-lookup"><span data-stu-id="e250c-101">Consider the following `ConfigureServices` method, which registers services and configures options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

<span data-ttu-id="e250c-102">Les groupes d’inscriptions associés peuvent être déplacés vers une méthode d’extension pour inscrire les services.</span><span class="sxs-lookup"><span data-stu-id="e250c-102">Related groups of registrations can be moved to an extension method to register services.</span></span> <span data-ttu-id="e250c-103">Par exemple, les services de configuration sont ajoutés à la classe suivante :</span><span class="sxs-lookup"><span data-stu-id="e250c-103">For example, the configuration services are added to the following class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

<span data-ttu-id="e250c-104">Les services restants sont inscrits dans une classe similaire.</span><span class="sxs-lookup"><span data-stu-id="e250c-104">The remaining services are registered in a similar class.</span></span> <span data-ttu-id="e250c-105">La `ConfigureServices` méthode suivante utilise les nouvelles méthodes d’extension pour inscrire les services :</span><span class="sxs-lookup"><span data-stu-id="e250c-105">The following `ConfigureServices` method uses the new extension methods to register the services:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

<span data-ttu-id="e250c-106">**_Remarque :_** Chaque `services.Add{GROUP_NAME}` méthode d’extension ajoute et configure éventuellement des services.</span><span class="sxs-lookup"><span data-stu-id="e250c-106">**_Note:_** Each `services.Add{GROUP_NAME}` extension method adds and potentially configures services.</span></span> <span data-ttu-id="e250c-107">Par exemple, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> ajoute les services MVC dont les vues ont besoin et <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> ajoute les pages de services Razor requis.</span><span class="sxs-lookup"><span data-stu-id="e250c-107">For example, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adds the services MVC controllers with views require, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adds the services Razor Pages requires.</span></span> <span data-ttu-id="e250c-108">Nous vous recommandons d’appliquer cette Convention d’affectation de noms aux applications.</span><span class="sxs-lookup"><span data-stu-id="e250c-108">We recommended that apps follow this naming convention.</span></span> <span data-ttu-id="e250c-109">Placez les méthodes d’extension dans l’espace de noms <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> pour encapsuler des groupes d’inscriptions de service.</span><span class="sxs-lookup"><span data-stu-id="e250c-109">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>
