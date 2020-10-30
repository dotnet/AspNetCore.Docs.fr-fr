---
title: API de protection des données diverses ASP.NET Core
author: rick-anderson
description: En savoir plus sur l’interface ISecret de protection des données ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: bd772b11300cca8896ed512da1cd12c49e6f104b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060753"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="6ffdf-103">API de protection des données diverses ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6ffdf-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="6ffdf-104">Les types qui implémentent l’une des interfaces suivantes doivent être thread-safe pour plusieurs appelants.</span><span class="sxs-lookup"><span data-stu-id="6ffdf-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="6ffdf-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="6ffdf-105">ISecret</span></span>

<span data-ttu-id="6ffdf-106">L' `ISecret` interface représente une valeur secrète, telle que le matériel de clé de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="6ffdf-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="6ffdf-107">Il contient la surface de l’API suivante :</span><span class="sxs-lookup"><span data-stu-id="6ffdf-107">It contains the following API surface:</span></span>

* <span data-ttu-id="6ffdf-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="6ffdf-108">`Length`: `int`</span></span>

* <span data-ttu-id="6ffdf-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="6ffdf-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="6ffdf-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="6ffdf-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="6ffdf-111">La `WriteSecretIntoBuffer` méthode remplit la mémoire tampon fournie avec la valeur de secret brut.</span><span class="sxs-lookup"><span data-stu-id="6ffdf-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="6ffdf-112">La raison pour laquelle cette API prend la mémoire tampon comme paramètre au lieu de retourner `byte[]` directement est que cela donne à l’appelant la possibilité d’épingler l’objet de mémoire tampon, ce qui limite l’exposition secrète au garbage collector géré.</span><span class="sxs-lookup"><span data-stu-id="6ffdf-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="6ffdf-113">Le `Secret` type est une implémentation concrète de `ISecret` où la valeur secrète est stockée dans la mémoire in-process.</span><span class="sxs-lookup"><span data-stu-id="6ffdf-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="6ffdf-114">Sur les plateformes Windows, la valeur de la clé secrète est chiffrée via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span><span class="sxs-lookup"><span data-stu-id="6ffdf-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](/windows/win32/api/dpapi/nf-dpapi-cryptprotectmemory).</span></span>
