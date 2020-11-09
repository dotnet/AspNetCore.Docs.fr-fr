---
title: Immuabilité des clés et paramètres de clé dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation des API d’immuabilité de la clé de protection des données ASP.NET Core.
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061247"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="06b6d-103">Immuabilité des clés et paramètres de clé dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="06b6d-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="06b6d-104">Une fois qu’un objet est rendu persistant dans le magasin de stockage, sa représentation est toujours fixe.</span><span class="sxs-lookup"><span data-stu-id="06b6d-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="06b6d-105">De nouvelles données peuvent être ajoutées au magasin de stockage, mais les données existantes ne peuvent jamais être mutées.</span><span class="sxs-lookup"><span data-stu-id="06b6d-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="06b6d-106">L’objectif principal de ce comportement est d’empêcher la corruption des données.</span><span class="sxs-lookup"><span data-stu-id="06b6d-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="06b6d-107">L’une des conséquences de ce comportement est qu’une fois qu’une clé est écrite dans le magasin de stockage, elle est immuable.</span><span class="sxs-lookup"><span data-stu-id="06b6d-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="06b6d-108">Ses dates de création, d’activation et d’expiration ne peuvent jamais être modifiées, même si elles peuvent être révoquées à l’aide de `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="06b6d-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="06b6d-109">En outre, ses informations algorithmiques sous-jacentes, le matériel de clé principale et le chiffrement au repos sont également immuables.</span><span class="sxs-lookup"><span data-stu-id="06b6d-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="06b6d-110">Si le développeur modifie un paramètre qui affecte la persistance des clés, ces modifications n’entrent pas en vigueur jusqu’à la prochaine génération d’une clé, par le biais d’un appel explicite à `IKeyManager.CreateNewKey` ou via le comportement de [génération automatique de la clé automatique](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) du système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="06b6d-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="06b6d-111">Les paramètres qui affectent la persistance des clés sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="06b6d-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="06b6d-112">Durée de vie de la clé par défaut</span><span class="sxs-lookup"><span data-stu-id="06b6d-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="06b6d-113">Mécanisme de chiffrement à clé au repos</span><span class="sxs-lookup"><span data-stu-id="06b6d-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="06b6d-114">Informations algorithmiques contenues dans la clé</span><span class="sxs-lookup"><span data-stu-id="06b6d-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="06b6d-115">Si vous avez besoin que ces paramètres démarrent avant la prochaine période de répercussion de clé automatique, envisagez d’effectuer un appel explicite à `IKeyManager.CreateNewKey` pour forcer la création d’une nouvelle clé.</span><span class="sxs-lookup"><span data-stu-id="06b6d-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="06b6d-116">N’oubliez pas de fournir une date d’activation explicite ({Now + 2 days} est une bonne règle empirique pour permettre le temps nécessaire à la propagation de la modification) et la date d’expiration dans l’appel.</span><span class="sxs-lookup"><span data-stu-id="06b6d-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="06b6d-117">Toutes les applications qui touchent le référentiel doivent spécifier les mêmes paramètres avec les `IDataProtectionBuilder` méthodes d’extension.</span><span class="sxs-lookup"><span data-stu-id="06b6d-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="06b6d-118">Dans le cas contraire, les propriétés de la clé persistante dépendent de l’application qui a appelé les routines de génération de clé.</span><span class="sxs-lookup"><span data-stu-id="06b6d-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
