---
title: Limiter la durée de vie des charges utiles protégées dans ASP.NET Core
author: rick-anderson
description: Découvrez comment limiter la durée de vie d’une charge utile protégée à l’aide des API de protection des données ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 74417d076399066a49271c27ff128d9de6c10f94
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060792"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a><span data-ttu-id="8477d-103">Limiter la durée de vie des charges utiles protégées dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8477d-103">Limit the lifetime of protected payloads in ASP.NET Core</span></span>

<span data-ttu-id="8477d-104">Il existe des scénarios dans lesquels le développeur d’applications souhaite créer une charge utile protégée qui expire après un laps de temps défini.</span><span class="sxs-lookup"><span data-stu-id="8477d-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="8477d-105">Par exemple, la charge utile protégée peut représenter un jeton de réinitialisation de mot de passe qui ne doit être valide que pendant une heure.</span><span class="sxs-lookup"><span data-stu-id="8477d-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="8477d-106">Il est certes possible pour le développeur de créer son propre format de charge utile qui contient une date d’expiration incorporée, et les développeurs avancés peuvent souhaiter le faire tout de même, mais pour la plupart des développeurs qui gèrent ces expirations peuvent devenir fastidieux.</span><span class="sxs-lookup"><span data-stu-id="8477d-106">It's certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="8477d-107">Pour faciliter cette tâche pour les développeurs, le package [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contient des API d’utilitaire pour créer des charges utiles qui expirent automatiquement après un laps de temps défini.</span><span class="sxs-lookup"><span data-stu-id="8477d-107">To make this easier for our developer audience, the package [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="8477d-108">Ces API se bloquent du `ITimeLimitedDataProtector` type.</span><span class="sxs-lookup"><span data-stu-id="8477d-108">These APIs hang off of the `ITimeLimitedDataProtector` type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="8477d-109">Utilisation des API</span><span class="sxs-lookup"><span data-stu-id="8477d-109">API usage</span></span>

<span data-ttu-id="8477d-110">L' `ITimeLimitedDataProtector` interface est l’interface principale pour la protection et la déprotection des charges utiles limitées dans le temps ou à l’expiration automatique.</span><span class="sxs-lookup"><span data-stu-id="8477d-110">The `ITimeLimitedDataProtector` interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="8477d-111">Pour créer une instance d’un `ITimeLimitedDataProtector` objet, vous avez d’abord besoin d’une instance d’un [IDataProtector](xref:security/data-protection/consumer-apis/overview) standard construit avec un objectif spécifique.</span><span class="sxs-lookup"><span data-stu-id="8477d-111">To create an instance of an `ITimeLimitedDataProtector`, you'll first need an instance of a regular [IDataProtector](xref:security/data-protection/consumer-apis/overview) constructed with a specific purpose.</span></span> <span data-ttu-id="8477d-112">Une fois l' `IDataProtector` instance disponible, appelez la `IDataProtector.ToTimeLimitedDataProtector` méthode d’extension pour obtenir un protecteur avec des fonctionnalités d’expiration intégrées.</span><span class="sxs-lookup"><span data-stu-id="8477d-112">Once the `IDataProtector` instance is available, call the `IDataProtector.ToTimeLimitedDataProtector` extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="8477d-113">`ITimeLimitedDataProtector` expose les méthodes d’extension et la surface d’API suivantes :</span><span class="sxs-lookup"><span data-stu-id="8477d-113">`ITimeLimitedDataProtector` exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="8477d-114">CreateProtector (String Purpose) : ITimeLimitedDataProtector : cette API est similaire à la existante `IDataProtectionProvider.CreateProtector` dans la mesure où elle peut être utilisée pour créer des [chaînes d’objectif](xref:security/data-protection/consumer-apis/purpose-strings) à partir d’un protecteur à durée limitée.</span><span class="sxs-lookup"><span data-stu-id="8477d-114">CreateProtector(string purpose) : ITimeLimitedDataProtector - This API is similar to the existing `IDataProtectionProvider.CreateProtector` in that it can be used to create [purpose chains](xref:security/data-protection/consumer-apis/purpose-strings) from a root time-limited protector.</span></span>

* <span data-ttu-id="8477d-115">Protect (Byte [] texte en clair, expiration DateTimeOffset) : Byte []</span><span class="sxs-lookup"><span data-stu-id="8477d-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="8477d-116">Protect (Byte [] texte en clair, durée de vie TimeSpan) : Byte []</span><span class="sxs-lookup"><span data-stu-id="8477d-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="8477d-117">Protect (Byte [] texte en clair) : Byte []</span><span class="sxs-lookup"><span data-stu-id="8477d-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="8477d-118">Protect (chaîne en texte clair, expiration DateTimeOffset) : chaîne</span><span class="sxs-lookup"><span data-stu-id="8477d-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="8477d-119">Protect (chaîne en clair, durée de vie de la période) : chaîne</span><span class="sxs-lookup"><span data-stu-id="8477d-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="8477d-120">Protect (chaîne de texte en clair) : chaîne</span><span class="sxs-lookup"><span data-stu-id="8477d-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="8477d-121">En plus des méthodes principales `Protect` qui prennent uniquement le texte en clair, il existe de nouvelles surcharges qui permettent de spécifier la date d’expiration de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="8477d-121">In addition to the core `Protect` methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="8477d-122">La date d’expiration peut être spécifiée sous la forme d’une date absolue (par `DateTimeOffset` exemple,) ou sous la forme d’un temps relatif (à partir de l’heure système actuelle, via un `TimeSpan` ).</span><span class="sxs-lookup"><span data-stu-id="8477d-122">The expiration date can be specified as an absolute date (via a `DateTimeOffset`) or as a relative time (from the current system time, via a `TimeSpan`).</span></span> <span data-ttu-id="8477d-123">Si une surcharge qui ne prend pas d’expiration est appelée, la charge utile est censée ne jamais expirer.</span><span class="sxs-lookup"><span data-stu-id="8477d-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="8477d-124">UnProtection (Byte [] protectedData, out DateTimeOffset expiration) : Byte []</span><span class="sxs-lookup"><span data-stu-id="8477d-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="8477d-125">Unprotect (Byte [] protectedData) : Byte []</span><span class="sxs-lookup"><span data-stu-id="8477d-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="8477d-126">UnProtection (String protectedData, out DateTimeOffset expiration) : chaîne</span><span class="sxs-lookup"><span data-stu-id="8477d-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="8477d-127">Unprotect (chaîne protectedData) : chaîne</span><span class="sxs-lookup"><span data-stu-id="8477d-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="8477d-128">Les `Unprotect` méthodes retournent les données non protégées d’origine.</span><span class="sxs-lookup"><span data-stu-id="8477d-128">The `Unprotect` methods return the original unprotected data.</span></span> <span data-ttu-id="8477d-129">Si la charge utile n’a pas encore expiré, l’expiration absolue est retournée en tant que paramètre de sortie facultatif avec les données non protégées d’origine.</span><span class="sxs-lookup"><span data-stu-id="8477d-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="8477d-130">Si la charge utile est expirée, toutes les surcharges de la méthode Unprotect lèvent CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="8477d-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="8477d-131">Il n’est pas recommandé d’utiliser ces API pour protéger les charges utiles qui nécessitent une persistance à long terme ou indéfinie.</span><span class="sxs-lookup"><span data-stu-id="8477d-131">It's not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="8477d-132">« Puis-je me permettre que les charges utiles protégées soient irrémédiablement irrémédiables après un mois ? »</span><span class="sxs-lookup"><span data-stu-id="8477d-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="8477d-133">peut servir de bonne règle de pouce ; Si la réponse n’est pas, les développeurs doivent envisager d’autres API.</span><span class="sxs-lookup"><span data-stu-id="8477d-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="8477d-134">L’exemple ci-dessous utilise les [chemins d’accès de code non-di](xref:security/data-protection/configuration/non-di-scenarios) pour instancier le système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="8477d-134">The sample below uses the [non-DI code paths](xref:security/data-protection/configuration/non-di-scenarios) for instantiating the data protection system.</span></span> <span data-ttu-id="8477d-135">Pour exécuter cet exemple, assurez-vous d’avoir d’abord ajouté une référence au package Microsoft. AspNetCore. DataProtection. extensions.</span><span class="sxs-lookup"><span data-stu-id="8477d-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
