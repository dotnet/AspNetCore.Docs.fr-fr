---
title: Prise en main des API de protection des données dans ASP.NET Core
author: rick-anderson
description: Découvrez comment utiliser les API de protection des données ASP.NET Core pour protéger et ôter la protection des données dans une application.
ms.author: riande
ms.date: 11/12/2019
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
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1f0d42a7b12edb870481024372d75cdc9e57be21
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051653"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="f72fc-103">Prise en main des API de protection des données dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f72fc-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="f72fc-104">Pour la plus simple, la protection des données comprend les étapes suivantes :</span><span class="sxs-lookup"><span data-stu-id="f72fc-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="f72fc-105">Créer un protecteur de données à partir d’un fournisseur de protection des données.</span><span class="sxs-lookup"><span data-stu-id="f72fc-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="f72fc-106">Appelez la `Protect` méthode avec les données que vous souhaitez protéger.</span><span class="sxs-lookup"><span data-stu-id="f72fc-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="f72fc-107">Appelez la `Unprotect` méthode avec les données que vous souhaitez réactiver en texte brut.</span><span class="sxs-lookup"><span data-stu-id="f72fc-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="f72fc-108">La plupart des infrastructures et des modèles d’application, tels que ASP.NET Core ou SignalR , configurent déjà le système de protection des données et l’ajoutent à un conteneur de service auquel vous accédez via l’injection de dépendances.</span><span class="sxs-lookup"><span data-stu-id="f72fc-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="f72fc-109">L’exemple suivant illustre la configuration d’un conteneur de service pour l’injection de dépendances et l’inscription de la pile de protection des données, la réception du fournisseur de protection des données via l’injection de dépendances, la création d’un protecteur et la protection, puis la déprotection des données.</span><span class="sxs-lookup"><span data-stu-id="f72fc-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="f72fc-110">Lorsque vous créez un protecteur, vous devez fournir une ou plusieurs [chaînes d’objectif](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="f72fc-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="f72fc-111">Une chaîne d’objectif assure l’isolement entre les consommateurs.</span><span class="sxs-lookup"><span data-stu-id="f72fc-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="f72fc-112">Par exemple, un protecteur créé avec une chaîne d’objectif « Green » ne serait pas en mesure d’ôter la protection des données fournies par un protecteur avec un objectif « violet ».</span><span class="sxs-lookup"><span data-stu-id="f72fc-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="f72fc-113">Les instances de `IDataProtectionProvider` et de `IDataProtector` sont thread-safe pour plusieurs appelants.</span><span class="sxs-lookup"><span data-stu-id="f72fc-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="f72fc-114">Il est prévu qu’une fois qu’un composant obtient une référence à un `IDataProtector` via un appel à `CreateProtector` , il utilise cette référence pour plusieurs appels à `Protect` et `Unprotect` .</span><span class="sxs-lookup"><span data-stu-id="f72fc-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="f72fc-115">Un appel à `Unprotect` lèvera CryptographicException si la charge utile protégée ne peut pas être vérifiée ou déchiffrée.</span><span class="sxs-lookup"><span data-stu-id="f72fc-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="f72fc-116">Certains composants peuvent souhaiter ignorer des erreurs lors de l’opération de non-protection. un composant qui lit les cookie s d’authentification peut gérer cette erreur et traiter la demande comme s’il avait un élément cookie au lieu de faire échouer la demande.</span><span class="sxs-lookup"><span data-stu-id="f72fc-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="f72fc-117">Les composants qui veulent ce comportement doivent intercepter spécifiquement CryptographicException au lieu d’absorber toutes les exceptions.</span><span class="sxs-lookup"><span data-stu-id="f72fc-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
