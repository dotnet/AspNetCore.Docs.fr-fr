---
title: Extensibilité du chiffrement de base dans ASP.NET Core
author: rick-anderson
description: En savoir plus sur IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer et la fabrique de niveau supérieur.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 99ce283d56a6010ddd846f21e0ca9a9a324d55fc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060779"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="fa5d5-103">Extensibilité du chiffrement de base dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa5d5-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="fa5d5-104">Les types qui implémentent l’une des interfaces suivantes doivent être thread-safe pour plusieurs appelants.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="fa5d5-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="fa5d5-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="fa5d5-106">L’interface **IAuthenticatedEncryptor** est le bloc de construction de base du sous-système de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="fa5d5-107">Il y a généralement un IAuthenticatedEncryptor par clé, et l’instance IAuthenticatedEncryptor encapsule tout le matériel de clé de chiffrement et les informations algorithmiques nécessaires pour effectuer des opérations de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="fa5d5-108">Comme son nom le suggère, le type est chargé de fournir des services de chiffrement et de déchiffrement authentifiés.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="fa5d5-109">Il expose les deux API suivantes.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="fa5d5-110">La méthode Encrypt retourne un objet BLOB qui comprend le texte en clair chiffré et une balise d’authentification.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="fa5d5-111">La balise d’authentification doit englober les données authentifiées supplémentaires (AAD), bien que le AAD lui-même ne doive pas être récupérable à partir de la charge utile finale.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="fa5d5-112">La méthode Decrypt valide la balise d’authentification et retourne la charge utile déchiffrée.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="fa5d5-113">Toutes les défaillances (sauf ArgumentNullException et similaires) doivent être homogénéisées en CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="fa5d5-114">L’instance IAuthenticatedEncryptor elle-même ne doit pas nécessairement contenir le matériel de clé.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="fa5d5-115">Par exemple, l’implémentation peut déléguer à un HSM pour toutes les opérations.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="fa5d5-116">Création d’un IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="fa5d5-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="fa5d5-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="fa5d5-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="fa5d5-118">L’interface **IAuthenticatedEncryptorFactory** représente un type qui sait comment créer une instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="fa5d5-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="fa5d5-119">Son API se présente comme suit.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-119">Its API is as follows.</span></span>

* <span data-ttu-id="fa5d5-120">CreateEncryptorInstance (la clé IKey) : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="fa5d5-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="fa5d5-121">Pour toute instance de IKey donnée, tous les chiffreurs authentifiés créés par sa méthode CreateEncryptorInstance doivent être considérés comme équivalents, comme dans l’exemple de code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="fa5d5-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="fa5d5-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="fa5d5-123">L’interface **IAuthenticatedEncryptorDescriptor** représente un type qui sait comment créer une instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) .</span><span class="sxs-lookup"><span data-stu-id="fa5d5-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="fa5d5-124">Son API se présente comme suit.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-124">Its API is as follows.</span></span>

* <span data-ttu-id="fa5d5-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="fa5d5-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="fa5d5-126">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="fa5d5-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="fa5d5-127">Comme IAuthenticatedEncryptor, une instance de IAuthenticatedEncryptorDescriptor est supposée encapsuler une clé spécifique.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="fa5d5-128">Cela signifie que pour toute instance IAuthenticatedEncryptorDescriptor donnée, tous les chiffreurs authentifiés créés par sa méthode CreateEncryptorInstance doivent être considérés comme équivalents, comme dans l’exemple de code ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="fa5d5-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2. x uniquement)</span><span class="sxs-lookup"><span data-stu-id="fa5d5-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="fa5d5-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="fa5d5-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="fa5d5-131">L’interface **IAuthenticatedEncryptorDescriptor** représente un type qui sait comment s’exporter au format XML.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="fa5d5-132">Son API se présente comme suit.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-132">Its API is as follows.</span></span>

* <span data-ttu-id="fa5d5-133">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="fa5d5-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="fa5d5-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="fa5d5-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="fa5d5-135">Sérialisation XML</span><span class="sxs-lookup"><span data-stu-id="fa5d5-135">XML Serialization</span></span>

<span data-ttu-id="fa5d5-136">La principale différence entre IAuthenticatedEncryptor et IAuthenticatedEncryptorDescriptor est que le descripteur sait comment créer le chiffreur et lui fournir des arguments valides.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="fa5d5-137">Prenons l’exemple d’un IAuthenticatedEncryptor dont l’implémentation s’appuie sur SymmetricAlgorithm et KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="fa5d5-138">Le travail du chiffreur consiste à consommer ces types, mais il n’est pas nécessairement conscient de l’origine de ces types, donc il ne peut pas véritablement écrire une description appropriée de la manière de se recréer si l’application redémarre.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="fa5d5-139">Le descripteur agit comme un niveau supérieur.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="fa5d5-140">Dans la mesure où le descripteur sait comment créer l’instance de chiffreur (par exemple, il sait comment créer les algorithmes requis), il peut sérialiser cette connaissance au format XML pour que l’instance de chiffreur puisse être recréée après la réinitialisation d’une application.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="fa5d5-141">Le descripteur peut être sérialisé par le biais de sa routine ExportToXml.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="fa5d5-142">Cette routine retourne un XmlSerializedDescriptorInfo qui contient deux propriétés : la représentation XElement du descripteur et le type qui représente un [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) qui peut être utilisé pour ressusciter ce descripteur en fonction du XElement correspondant.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="fa5d5-143">Le descripteur sérialisé peut contenir des informations sensibles telles que le matériel de clé de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="fa5d5-144">Le système de protection des données offre une prise en charge intégrée du chiffrement des informations avant qu’elles ne soient rendues persistantes dans le stockage.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="fa5d5-145">Pour tirer parti de cela, le descripteur doit marquer l’élément qui contient des informations sensibles avec le nom d’attribut « requiresEncryption » (xmlns « <http://schemas.asp.net/2015/03/dataProtection> »), la valeur « true ».</span><span class="sxs-lookup"><span data-stu-id="fa5d5-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="fa5d5-146">Il existe une API d’assistance pour définir cet attribut.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="fa5d5-147">Appelez la méthode d’extension XElement. MarkAsRequiresEncryption () située dans l’espace de noms Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="fa5d5-148">Il peut également y avoir des cas où le descripteur sérialisé ne contient pas d’informations sensibles.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="fa5d5-149">Reprenons le cas d’une clé de chiffrement stockée dans un HSM.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="fa5d5-150">Le descripteur ne peut pas écrire le matériel de clé lors de la sérialisation, car le HSM n’expose pas les éléments sous forme de texte en clair.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="fa5d5-151">Au lieu de cela, le descripteur peut écrire la version encapsulée de la clé (si le HSM autorise l’exportation de cette manière) ou l’identificateur unique du HSM pour la clé.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="fa5d5-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="fa5d5-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="fa5d5-153">L’interface **IAuthenticatedEncryptorDescriptorDeserializer** représente un type qui sait comment désérialiser une instance IAuthenticatedEncryptorDescriptor à partir d’un XElement.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="fa5d5-154">Il expose une méthode unique :</span><span class="sxs-lookup"><span data-stu-id="fa5d5-154">It exposes a single method:</span></span>

* <span data-ttu-id="fa5d5-155">ImportFromXml (élément XElement) : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="fa5d5-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="fa5d5-156">La méthode ImportFromXml prend le XElement qui a été retourné par [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) et crée un équivalent du IAuthenticatedEncryptorDescriptor d’origine.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="fa5d5-157">Les types qui implémentent IAuthenticatedEncryptorDescriptorDeserializer doivent avoir l’un des deux constructeurs publics suivants :</span><span class="sxs-lookup"><span data-stu-id="fa5d5-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="fa5d5-158">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="fa5d5-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="fa5d5-159">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="fa5d5-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="fa5d5-160">Le IServiceProvider passé au constructeur peut avoir la valeur null.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="fa5d5-161">La fabrique de niveau supérieur</span><span class="sxs-lookup"><span data-stu-id="fa5d5-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="fa5d5-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="fa5d5-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="fa5d5-163">La classe **AlgorithmConfiguration** représente un type qui sait comment créer des instances [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="fa5d5-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="fa5d5-164">Il expose une seule API.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-164">It exposes a single API.</span></span>

* <span data-ttu-id="fa5d5-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="fa5d5-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="fa5d5-166">Imaginez AlgorithmConfiguration comme fabrique de niveau supérieur.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="fa5d5-167">La configuration sert de modèle.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-167">The configuration serves as a template.</span></span> <span data-ttu-id="fa5d5-168">Elle encapsule les informations algorithmiques (par exemple, cette configuration génère des descripteurs avec une clé principale AES-128-GCM), mais elle n’est pas encore associée à une clé spécifique.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="fa5d5-169">Quand CreateNewDescriptor est appelé, le matériel de clé actualisé est créé uniquement pour cet appel, et un nouveau IAuthenticatedEncryptorDescriptor est généré, ce qui encapsule ce matériel de clé et les informations algorithmiques requises pour consommer le matériel.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="fa5d5-170">Le matériel de clé peut être créé dans le logiciel (et conservé en mémoire), il peut être créé et conservé dans un HSM, et ainsi de suite.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="fa5d5-171">Le point essentiel est que les deux appels à CreateNewDescriptor ne doivent jamais créer des instances IAuthenticatedEncryptorDescriptor équivalentes.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="fa5d5-172">Le type AlgorithmConfiguration sert de point d’entrée pour les routines de création de clés telles que le [déroulement automatique de clés](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="fa5d5-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="fa5d5-173">Pour modifier l’implémentation pour toutes les futures clés, définissez la propriété AuthenticatedEncryptorConfiguration dans KeyManagementOptions.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="fa5d5-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="fa5d5-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="fa5d5-175">L’interface **IAuthenticatedEncryptorConfiguration** représente un type qui sait comment créer des instances [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) .</span><span class="sxs-lookup"><span data-stu-id="fa5d5-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="fa5d5-176">Il expose une seule API.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-176">It exposes a single API.</span></span>

* <span data-ttu-id="fa5d5-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="fa5d5-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="fa5d5-178">Imaginez IAuthenticatedEncryptorConfiguration comme fabrique de niveau supérieur.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="fa5d5-179">La configuration sert de modèle.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-179">The configuration serves as a template.</span></span> <span data-ttu-id="fa5d5-180">Elle encapsule les informations algorithmiques (par exemple, cette configuration génère des descripteurs avec une clé principale AES-128-GCM), mais elle n’est pas encore associée à une clé spécifique.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="fa5d5-181">Quand CreateNewDescriptor est appelé, le matériel de clé actualisé est créé uniquement pour cet appel, et un nouveau IAuthenticatedEncryptorDescriptor est généré, ce qui encapsule ce matériel de clé et les informations algorithmiques requises pour consommer le matériel.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="fa5d5-182">Le matériel de clé peut être créé dans le logiciel (et conservé en mémoire), il peut être créé et conservé dans un HSM, et ainsi de suite.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="fa5d5-183">Le point essentiel est que les deux appels à CreateNewDescriptor ne doivent jamais créer des instances IAuthenticatedEncryptorDescriptor équivalentes.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="fa5d5-184">Le type IAuthenticatedEncryptorConfiguration sert de point d’entrée pour les routines de création de clés telles que le [déroulement automatique de clés](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="fa5d5-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="fa5d5-185">Pour modifier l’implémentation pour toutes les futures clés, inscrivez un IAuthenticatedEncryptorConfiguration Singleton dans le conteneur de service.</span><span class="sxs-lookup"><span data-stu-id="fa5d5-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
