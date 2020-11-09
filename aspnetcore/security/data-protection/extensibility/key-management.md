---
title: Extensibilité de la gestion des clés dans ASP.NET Core
author: rick-anderson
description: En savoir plus sur ASP.NET Core extensibilité de la gestion des clés de protection des données.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 08ec018238d18cd253e793267c5b146df8319485
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060766"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="a6110-103">Extensibilité de la gestion des clés dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6110-103">Key management extensibility in ASP.NET Core</span></span>

<span data-ttu-id="a6110-104">Lisez la section [gestion des clés](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) avant de lire cette section, car elle explique certains concepts fondamentaux sous-jacents à ces API.</span><span class="sxs-lookup"><span data-stu-id="a6110-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

<span data-ttu-id="a6110-105">**Avertissement** : les types qui implémentent l’une des interfaces suivantes doivent être thread-safe pour plusieurs appelants.</span><span class="sxs-lookup"><span data-stu-id="a6110-105">**Warning** : Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="a6110-106">Clé :</span><span class="sxs-lookup"><span data-stu-id="a6110-106">Key</span></span>

<span data-ttu-id="a6110-107">L' `IKey` interface est la représentation de base d’une clé dans chiffrement.</span><span class="sxs-lookup"><span data-stu-id="a6110-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="a6110-108">Le terme clé est utilisé ici dans le sens abstrait, et non dans le sens littéral du « matériel de clé de chiffrement ».</span><span class="sxs-lookup"><span data-stu-id="a6110-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="a6110-109">Une clé a les propriétés suivantes :</span><span class="sxs-lookup"><span data-stu-id="a6110-109">A key has the following properties:</span></span>

* <span data-ttu-id="a6110-110">Dates d’activation, de création et d’expiration</span><span class="sxs-lookup"><span data-stu-id="a6110-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="a6110-111">État de révocation.</span><span class="sxs-lookup"><span data-stu-id="a6110-111">Revocation status</span></span>

* <span data-ttu-id="a6110-112">Identificateur de clé (GUID)</span><span class="sxs-lookup"><span data-stu-id="a6110-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="a6110-113">En outre, `IKey` expose une `CreateEncryptor` méthode qui peut être utilisée pour créer une instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) liée à cette clé.</span><span class="sxs-lookup"><span data-stu-id="a6110-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="a6110-114">En outre, `IKey` expose une `CreateEncryptorInstance` méthode qui peut être utilisée pour créer une instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) liée à cette clé.</span><span class="sxs-lookup"><span data-stu-id="a6110-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a6110-115">Il n’existe aucune API pour récupérer le matériel de chiffrement brut à partir d’une `IKey` instance.</span><span class="sxs-lookup"><span data-stu-id="a6110-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="a6110-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="a6110-116">IKeyManager</span></span>

<span data-ttu-id="a6110-117">L' `IKeyManager` interface représente un objet responsable du stockage, de la récupération et de la manipulation des clés générales.</span><span class="sxs-lookup"><span data-stu-id="a6110-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="a6110-118">Il expose trois opérations de haut niveau :</span><span class="sxs-lookup"><span data-stu-id="a6110-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="a6110-119">Créez une nouvelle clé et conservez-la dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="a6110-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="a6110-120">Récupération de toutes les clés à partir du stockage.</span><span class="sxs-lookup"><span data-stu-id="a6110-120">Get all keys from storage.</span></span>

* <span data-ttu-id="a6110-121">Révoquez une ou plusieurs clés et conservez les informations de révocation dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="a6110-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="a6110-122">L’écriture d’un `IKeyManager` est une tâche très avancée et la majorité des développeurs ne doivent pas la tenter.</span><span class="sxs-lookup"><span data-stu-id="a6110-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="a6110-123">Au lieu de cela, la plupart des développeurs doivent tirer parti des fonctionnalités offertes par la classe [XmlKeyManager](#xmlkeymanager) .</span><span class="sxs-lookup"><span data-stu-id="a6110-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="a6110-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="a6110-124">XmlKeyManager</span></span>

<span data-ttu-id="a6110-125">Le `XmlKeyManager` type est l’implémentation concrète de `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="a6110-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="a6110-126">Il offre plusieurs fonctionnalités utiles, dont le dépôt de clés et le chiffrement des clés au repos.</span><span class="sxs-lookup"><span data-stu-id="a6110-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="a6110-127">Les clés de ce système sont représentées en tant qu’éléments XML (en particulier, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="a6110-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="a6110-128">`XmlKeyManager` dépend de plusieurs autres composants dans le cadre de la réalisation de ses tâches :</span><span class="sxs-lookup"><span data-stu-id="a6110-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="a6110-129">`AlgorithmConfiguration`, qui dicte les algorithmes utilisés par les nouvelles clés.</span><span class="sxs-lookup"><span data-stu-id="a6110-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="a6110-130">`IXmlRepository`, qui contrôle les emplacements où les clés sont conservées dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="a6110-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="a6110-131">`IXmlEncryptor` [facultatif], qui autorise le chiffrement des clés au repos.</span><span class="sxs-lookup"><span data-stu-id="a6110-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="a6110-132">`IKeyEscrowSink` [facultatif], qui fournit des services de dépôt de clés.</span><span class="sxs-lookup"><span data-stu-id="a6110-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="a6110-133">`IXmlRepository`, qui contrôle les emplacements où les clés sont conservées dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="a6110-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="a6110-134">`IXmlEncryptor` [facultatif], qui autorise le chiffrement des clés au repos.</span><span class="sxs-lookup"><span data-stu-id="a6110-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="a6110-135">`IKeyEscrowSink` [facultatif], qui fournit des services de dépôt de clés.</span><span class="sxs-lookup"><span data-stu-id="a6110-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="a6110-136">Vous trouverez ci-dessous des diagrammes de haut niveau qui indiquent la façon dont ces composants sont connectés entre eux `XmlKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="a6110-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Création de la clé](key-management/_static/keycreation2.png)

<span data-ttu-id="a6110-138">*Création de clé/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="a6110-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="a6110-139">Dans l’implémentation de `CreateNewKey` , le `AlgorithmConfiguration` composant est utilisé pour créer un unique `IAuthenticatedEncryptorDescriptor` , qui est ensuite sérialisé au format XML.</span><span class="sxs-lookup"><span data-stu-id="a6110-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="a6110-140">Si un récepteur de dépôt de clé est présent, le XML brut (non chiffré) est fourni au récepteur pour le stockage à long terme.</span><span class="sxs-lookup"><span data-stu-id="a6110-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="a6110-141">Le XML non chiffré est ensuite exécuté à l’aide d’un `IXmlEncryptor` (si nécessaire) pour générer le document XML chiffré.</span><span class="sxs-lookup"><span data-stu-id="a6110-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="a6110-142">Ce document chiffré est conservé dans un stockage à long terme via le `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="a6110-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="a6110-143">(Si aucun `IXmlEncryptor` n’est configuré, le document non chiffré est conservé dans le `IXmlRepository` .)</span><span class="sxs-lookup"><span data-stu-id="a6110-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Récupération de clé](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Création de la clé](key-management/_static/keycreation1.png)

<span data-ttu-id="a6110-146">*Création de clé/CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="a6110-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="a6110-147">Dans l’implémentation de `CreateNewKey` , le `IAuthenticatedEncryptorConfiguration` composant est utilisé pour créer un unique `IAuthenticatedEncryptorDescriptor` , qui est ensuite sérialisé au format XML.</span><span class="sxs-lookup"><span data-stu-id="a6110-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="a6110-148">Si un récepteur de dépôt de clé est présent, le XML brut (non chiffré) est fourni au récepteur pour le stockage à long terme.</span><span class="sxs-lookup"><span data-stu-id="a6110-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="a6110-149">Le XML non chiffré est ensuite exécuté à l’aide d’un `IXmlEncryptor` (si nécessaire) pour générer le document XML chiffré.</span><span class="sxs-lookup"><span data-stu-id="a6110-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="a6110-150">Ce document chiffré est conservé dans un stockage à long terme via le `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="a6110-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="a6110-151">(Si aucun `IXmlEncryptor` n’est configuré, le document non chiffré est conservé dans le `IXmlRepository` .)</span><span class="sxs-lookup"><span data-stu-id="a6110-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Récupération de clé](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="a6110-153">*Récupération de clé/GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="a6110-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="a6110-154">Dans l’implémentation de `GetAllKeys` , les documents XML représentant les clés et les révocations sont lus à partir du sous-jacent `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="a6110-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="a6110-155">Si ces documents sont chiffrés, le système les déchiffre automatiquement.</span><span class="sxs-lookup"><span data-stu-id="a6110-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="a6110-156">`XmlKeyManager` crée les `IAuthenticatedEncryptorDescriptorDeserializer` instances appropriées pour désérialiser les documents en `IAuthenticatedEncryptorDescriptor` instances, qui sont ensuite encapsulées dans des `IKey` instances individuelles.</span><span class="sxs-lookup"><span data-stu-id="a6110-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="a6110-157">Cette collection d' `IKey` instances est retournée à l’appelant.</span><span class="sxs-lookup"><span data-stu-id="a6110-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="a6110-158">Vous trouverez plus d’informations sur les éléments XML particuliers dans le [document de format de stockage de clés](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="a6110-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="a6110-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="a6110-159">IXmlRepository</span></span>

<span data-ttu-id="a6110-160">L' `IXmlRepository` interface représente un type qui peut conserver XML dans un magasin de stockage et récupérer du code XML.</span><span class="sxs-lookup"><span data-stu-id="a6110-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="a6110-161">Il expose deux API :</span><span class="sxs-lookup"><span data-stu-id="a6110-161">It exposes two APIs:</span></span>

* <span data-ttu-id="a6110-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="a6110-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="a6110-163">Les implémentations de `IXmlRepository` n’ont pas besoin d’analyser le XML qui les traverse.</span><span class="sxs-lookup"><span data-stu-id="a6110-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="a6110-164">Ils doivent traiter les documents XML comme opaques et laisser les couches supérieures se soucier de la génération et de l’analyse des documents.</span><span class="sxs-lookup"><span data-stu-id="a6110-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="a6110-165">Il existe quatre types concrets intégrés qui implémentent `IXmlRepository` :</span><span class="sxs-lookup"><span data-stu-id="a6110-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="a6110-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="a6110-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="a6110-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="a6110-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="a6110-168">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="a6110-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="a6110-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="a6110-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="a6110-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="a6110-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="a6110-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="a6110-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="a6110-172">AzureStorage. AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="a6110-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="a6110-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="a6110-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="a6110-174">Pour plus d’informations, consultez le document sur les [fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers) .</span><span class="sxs-lookup"><span data-stu-id="a6110-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="a6110-175">L’inscription d’un personnalisé `IXmlRepository` est appropriée lors de l’utilisation d’un autre magasin de stockage (par exemple, stockage table Azure).</span><span class="sxs-lookup"><span data-stu-id="a6110-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="a6110-176">Pour modifier le référentiel par défaut au niveau de l’application, inscrivez une `IXmlRepository` instance personnalisée :</span><span class="sxs-lookup"><span data-stu-id="a6110-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="a6110-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="a6110-177">IXmlEncryptor</span></span>

<span data-ttu-id="a6110-178">L' `IXmlEncryptor` interface représente un type qui peut chiffrer un élément XML en texte brut.</span><span class="sxs-lookup"><span data-stu-id="a6110-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="a6110-179">Il expose une seule API :</span><span class="sxs-lookup"><span data-stu-id="a6110-179">It exposes a single API:</span></span>

* <span data-ttu-id="a6110-180">Encrypt (XElement plaintextElement) : EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="a6110-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="a6110-181">Si un sérialisé `IAuthenticatedEncryptorDescriptor` contient des éléments marqués comme « nécessite un chiffrement », `XmlKeyManager` exécute ces éléments par le biais de la `IXmlEncryptor` méthode de configurée `Encrypt` , et il conserve l’élément chiffré plutôt que l’élément de texte brut dans le `IXmlRepository` .</span><span class="sxs-lookup"><span data-stu-id="a6110-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="a6110-182">La sortie de la `Encrypt` méthode est un `EncryptedXmlInfo` objet.</span><span class="sxs-lookup"><span data-stu-id="a6110-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="a6110-183">Cet objet est un wrapper qui contient à la fois le chiffrement obtenu `XElement` et le type qui représente un `IXmlDecryptor` qui peut être utilisé pour déchiffrer l’élément correspondant.</span><span class="sxs-lookup"><span data-stu-id="a6110-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="a6110-184">Il existe quatre types concrets intégrés qui implémentent `IXmlEncryptor` :</span><span class="sxs-lookup"><span data-stu-id="a6110-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="a6110-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="a6110-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="a6110-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="a6110-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="a6110-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="a6110-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="a6110-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="a6110-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="a6110-189">Pour plus d’informations, consultez le [document chiffrement à clé au repos](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="a6110-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="a6110-190">Pour modifier le mécanisme par défaut de chiffrement par clé au repos à l’ensemble de l’application, inscrivez une `IXmlEncryptor` instance personnalisée :</span><span class="sxs-lookup"><span data-stu-id="a6110-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="a6110-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="a6110-191">IXmlDecryptor</span></span>

<span data-ttu-id="a6110-192">L' `IXmlDecryptor` interface représente un type qui sait comment déchiffrer un `XElement` qui a été chiffré via un `IXmlEncryptor` .</span><span class="sxs-lookup"><span data-stu-id="a6110-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="a6110-193">Il expose une seule API :</span><span class="sxs-lookup"><span data-stu-id="a6110-193">It exposes a single API:</span></span>

* <span data-ttu-id="a6110-194">Decrypt (XElement encryptedElement) : XElement</span><span class="sxs-lookup"><span data-stu-id="a6110-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="a6110-195">La `Decrypt` méthode annule le chiffrement effectué par `IXmlEncryptor.Encrypt` .</span><span class="sxs-lookup"><span data-stu-id="a6110-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="a6110-196">En règle générale, chaque implémentation concrète aura `IXmlEncryptor` une implémentation concrète correspondante `IXmlDecryptor` .</span><span class="sxs-lookup"><span data-stu-id="a6110-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="a6110-197">Les types qui implémentent `IXmlDecryptor` doivent avoir l’un des deux constructeurs publics suivants :</span><span class="sxs-lookup"><span data-stu-id="a6110-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="a6110-198">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="a6110-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="a6110-199">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="a6110-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="a6110-200">Le `IServiceProvider` passé au constructeur peut avoir la valeur null.</span><span class="sxs-lookup"><span data-stu-id="a6110-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="a6110-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="a6110-201">IKeyEscrowSink</span></span>

<span data-ttu-id="a6110-202">L' `IKeyEscrowSink` interface représente un type qui peut effectuer un tiers de confiance d’informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="a6110-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="a6110-203">Rappelez-vous que les descripteurs sérialisés peuvent contenir des informations sensibles (telles que le matériel de chiffrement) et c’est ce qui a conduit à l’introduction du type [IXmlEncryptor](#ixmlencryptor) en premier lieu.</span><span class="sxs-lookup"><span data-stu-id="a6110-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="a6110-204">Toutefois, les accidents se produisent et les sonneries de clé peuvent être supprimées ou endommagées.</span><span class="sxs-lookup"><span data-stu-id="a6110-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="a6110-205">L’interface du tiers de confiance fournit un hachage d’échappement d’urgence, permettant d’accéder au code XML sérialisé brut avant qu’il ne soit transformé par un [IXmlEncryptor](#ixmlencryptor)configuré.</span><span class="sxs-lookup"><span data-stu-id="a6110-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="a6110-206">L’interface expose une seule API :</span><span class="sxs-lookup"><span data-stu-id="a6110-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="a6110-207">Store (GUID keyId, élément XElement)</span><span class="sxs-lookup"><span data-stu-id="a6110-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="a6110-208">Il s’agit de l' `IKeyEscrowSink` implémentation pour gérer l’élément fourni de manière sécurisée et cohérente avec la stratégie d’entreprise.</span><span class="sxs-lookup"><span data-stu-id="a6110-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="a6110-209">Une implémentation possible peut être que le récepteur de tiers de confiance chiffre l’élément XML à l’aide d’un certificat X. 509 d’entreprise connu dans lequel la clé privée du certificat a été déposée. le `CertificateXmlEncryptor` type peut vous aider.</span><span class="sxs-lookup"><span data-stu-id="a6110-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="a6110-210">L' `IKeyEscrowSink` implémentation est également chargée de rendre l’élément fourni de manière appropriée.</span><span class="sxs-lookup"><span data-stu-id="a6110-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="a6110-211">Par défaut, aucun mécanisme tiers de confiance n’est activé, bien que les administrateurs de serveur puissent [le configurer globalement](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="a6110-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="a6110-212">Elle peut également être configurée par programme à l’aide de la `IDataProtectionBuilder.AddKeyEscrowSink` méthode, comme illustré dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="a6110-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="a6110-213">Les `AddKeyEscrowSink` surcharges de méthode reflètent `IServiceCollection.AddSingleton` les `IServiceCollection.AddInstance` surcharges et, car les `IKeyEscrowSink` instances sont destinées à être des singletons.</span><span class="sxs-lookup"><span data-stu-id="a6110-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="a6110-214">Si plusieurs `IKeyEscrowSink` instances sont inscrites, chacune sera appelée pendant la génération de la clé, de sorte que les clés peuvent être déposées simultanément sur plusieurs mécanismes.</span><span class="sxs-lookup"><span data-stu-id="a6110-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="a6110-215">Il n’existe aucune API pour lire des documents à partir d’une `IKeyEscrowSink` instance.</span><span class="sxs-lookup"><span data-stu-id="a6110-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="a6110-216">Cela est cohérent avec la théorie de la conception du mécanisme de dépôt : il est conçu pour rendre le matériel de clé accessible à une autorité de confiance, et puisque l’application n’est pas elle-même une autorité approuvée, elle ne doit pas avoir accès à ses propres documents de confiance.</span><span class="sxs-lookup"><span data-stu-id="a6110-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="a6110-217">L’exemple de code suivant illustre la création et l’enregistrement d’un `IKeyEscrowSink` dans lequel les clés sont déposées de sorte que seuls les membres de « CONTOSODomain admins » puissent les récupérer.</span><span class="sxs-lookup"><span data-stu-id="a6110-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="a6110-218">Pour exécuter cet exemple, vous devez être sur un ordinateur Windows 8/Windows Server 2012 joint à un domaine, et le contrôleur de domaine doit être Windows Server 2012 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="a6110-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
