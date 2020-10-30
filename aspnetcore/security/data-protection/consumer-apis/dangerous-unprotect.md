---
title: Annuler la protection des charges utiles dont les clés ont été révoquées dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ôter la protection des données, protégées par des clés qui ont été révoquées, dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5f13b53182f720ac8b58d90701561d381981308a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051094"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="2fb2f-103">Annuler la protection des charges utiles dont les clés ont été révoquées dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fb2f-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="2fb2f-104">Les API de protection des données ASP.NET Core ne sont pas principalement destinées à la persistance illimitée des charges utiles confidentielles.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="2fb2f-105">D’autres technologies telles que [DPAPI Windows CNG](/windows/win32/seccng/cng-dpapi) et [Azure Rights Management](/rights-management/) sont plus adaptées au scénario de stockage indéfini, et elles ont des fonctionnalités de gestion de clés fortes.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-105">Other technologies like [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="2fb2f-106">Cela dit, rien n’empêche un développeur d’utiliser les API de protection des données ASP.NET Core pour la protection à long terme des données confidentielles.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="2fb2f-107">Les clés ne sont jamais supprimées de l’anneau de clé `IDataProtector.Unprotect` . elles peuvent donc toujours récupérer les charges utiles existantes tant que les clés sont disponibles et valides.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="2fb2f-108">Toutefois, un problème survient lorsque le développeur tente de déprotéger des données qui ont été protégées par une clé révoquée, comme `IDataProtector.Unprotect` lèvera une exception dans ce cas.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="2fb2f-109">Cela peut être utile pour les charges utiles courtes ou transitoires (comme les jetons d’authentification), car ces types de charges peuvent être facilement recréés par le système, et au pire, le visiteur du site peut être amené à se reconnecter.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="2fb2f-110">Toutefois, pour les charges utiles persistantes, l’utilisation de `Unprotect` throw peut entraîner une perte de données inacceptable.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="2fb2f-111">IPersistedDataProtector</span><span class="sxs-lookup"><span data-stu-id="2fb2f-111">IPersistedDataProtector</span></span>

<span data-ttu-id="2fb2f-112">Pour prendre en charge le scénario permettant d’empêcher la protection des charges utiles, même s’il s’agit de clés révoquées, le système de protection des données contient un `IPersistedDataProtector` type.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="2fb2f-113">Pour récupérer une instance de `IPersistedDataProtector` , récupérez simplement une instance de de `IDataProtector` manière normale et essayez d’effectuer un cast de `IDataProtector` en `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="2fb2f-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="2fb2f-114">Toutes les `IDataProtector` instances ne peuvent pas être converties en `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="2fb2f-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="2fb2f-115">Les développeurs doivent utiliser l’opérateur as C# ou similaire pour éviter les exceptions d’exécution provoquées par des casts non valides, et ils doivent être prêts à gérer le cas d’échec de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="2fb2f-116">`IPersistedDataProtector` expose la surface d’API suivante :</span><span class="sxs-lookup"><span data-stu-id="2fb2f-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="2fb2f-117">Cette API prend la charge utile protégée (comme un tableau d’octets) et retourne la charge utile non protégée.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="2fb2f-118">Il n’existe pas de surcharge basée sur une chaîne.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-118">There's no string-based overload.</span></span> <span data-ttu-id="2fb2f-119">Les deux paramètres out sont les suivants.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="2fb2f-120">`requiresMigration`: aura la valeur true si la clé utilisée pour protéger cette charge utile n’est plus la clé par défaut active, par exemple, la clé utilisée pour protéger cette charge utile est ancienne et une opération de déploiement de clé a eu lieu depuis.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="2fb2f-121">L’appelant peut souhaiter envisager de reprotéger la charge utile en fonction des besoins de l’entreprise.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="2fb2f-122">`wasRevoked`: aura la valeur true si la clé utilisée pour protéger cette charge utile a été révoquée.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="2fb2f-123">Soyez extrêmement vigilant lorsque `ignoreRevocationErrors: true` vous passez à la `DangerousUnprotect` méthode.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="2fb2f-124">Si, après avoir appelé cette méthode `wasRevoked` , la valeur est true, la clé utilisée pour protéger cette charge utile a été révoquée et l’authenticité de la charge utile doit être considérée comme suspecte.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="2fb2f-125">Dans ce cas, continue à fonctionner sur la charge utile non protégée si vous avez un certain niveau d’authenticité, par exemple s’il provient d’une base de données sécurisée, plutôt que d’être envoyée par un client Web non approuvé.</span><span class="sxs-lookup"><span data-stu-id="2fb2f-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
