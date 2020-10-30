---
title: Extensibilité du chiffrement de base dans ASP.NET Core
author: rick-anderson
description: En savoir plus sur IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer et la fabrique de niveau supérieur.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 99ce283d56a6010ddd846f21e0ca9a9a324d55fc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060779"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Extensibilité du chiffrement de base dans ASP.NET Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Les types qui implémentent l’une des interfaces suivantes doivent être thread-safe pour plusieurs appelants.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

L’interface **IAuthenticatedEncryptor** est le bloc de construction de base du sous-système de chiffrement. Il y a généralement un IAuthenticatedEncryptor par clé, et l’instance IAuthenticatedEncryptor encapsule tout le matériel de clé de chiffrement et les informations algorithmiques nécessaires pour effectuer des opérations de chiffrement.

Comme son nom le suggère, le type est chargé de fournir des services de chiffrement et de déchiffrement authentifiés. Il expose les deux API suivantes.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

La méthode Encrypt retourne un objet BLOB qui comprend le texte en clair chiffré et une balise d’authentification. La balise d’authentification doit englober les données authentifiées supplémentaires (AAD), bien que le AAD lui-même ne doive pas être récupérable à partir de la charge utile finale. La méthode Decrypt valide la balise d’authentification et retourne la charge utile déchiffrée. Toutes les défaillances (sauf ArgumentNullException et similaires) doivent être homogénéisées en CryptographicException.

> [!NOTE]
> L’instance IAuthenticatedEncryptor elle-même ne doit pas nécessairement contenir le matériel de clé. Par exemple, l’implémentation peut déléguer à un HSM pour toutes les opérations.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Création d’un IAuthenticatedEncryptor

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

L’interface **IAuthenticatedEncryptorFactory** représente un type qui sait comment créer une instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Son API se présente comme suit.

* CreateEncryptorInstance (la clé IKey) : IAuthenticatedEncryptor

Pour toute instance de IKey donnée, tous les chiffreurs authentifiés créés par sa méthode CreateEncryptorInstance doivent être considérés comme équivalents, comme dans l’exemple de code ci-dessous.

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

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

L’interface **IAuthenticatedEncryptorDescriptor** représente un type qui sait comment créer une instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) . Son API se présente comme suit.

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Comme IAuthenticatedEncryptor, une instance de IAuthenticatedEncryptorDescriptor est supposée encapsuler une clé spécifique. Cela signifie que pour toute instance IAuthenticatedEncryptorDescriptor donnée, tous les chiffreurs authentifiés créés par sa méthode CreateEncryptorInstance doivent être considérés comme équivalents, comme dans l’exemple de code ci-dessous.

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor (ASP.NET Core 2. x uniquement)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

L’interface **IAuthenticatedEncryptorDescriptor** représente un type qui sait comment s’exporter au format XML. Son API se présente comme suit.

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>Sérialisation XML

La principale différence entre IAuthenticatedEncryptor et IAuthenticatedEncryptorDescriptor est que le descripteur sait comment créer le chiffreur et lui fournir des arguments valides. Prenons l’exemple d’un IAuthenticatedEncryptor dont l’implémentation s’appuie sur SymmetricAlgorithm et KeyedHashAlgorithm. Le travail du chiffreur consiste à consommer ces types, mais il n’est pas nécessairement conscient de l’origine de ces types, donc il ne peut pas véritablement écrire une description appropriée de la manière de se recréer si l’application redémarre. Le descripteur agit comme un niveau supérieur. Dans la mesure où le descripteur sait comment créer l’instance de chiffreur (par exemple, il sait comment créer les algorithmes requis), il peut sérialiser cette connaissance au format XML pour que l’instance de chiffreur puisse être recréée après la réinitialisation d’une application.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Le descripteur peut être sérialisé par le biais de sa routine ExportToXml. Cette routine retourne un XmlSerializedDescriptorInfo qui contient deux propriétés : la représentation XElement du descripteur et le type qui représente un [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) qui peut être utilisé pour ressusciter ce descripteur en fonction du XElement correspondant.

Le descripteur sérialisé peut contenir des informations sensibles telles que le matériel de clé de chiffrement. Le système de protection des données offre une prise en charge intégrée du chiffrement des informations avant qu’elles ne soient rendues persistantes dans le stockage. Pour tirer parti de cela, le descripteur doit marquer l’élément qui contient des informations sensibles avec le nom d’attribut « requiresEncryption » (xmlns « <http://schemas.asp.net/2015/03/dataProtection> »), la valeur « true ».

>[!TIP]
> Il existe une API d’assistance pour définir cet attribut. Appelez la méthode d’extension XElement. MarkAsRequiresEncryption () située dans l’espace de noms Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.

Il peut également y avoir des cas où le descripteur sérialisé ne contient pas d’informations sensibles. Reprenons le cas d’une clé de chiffrement stockée dans un HSM. Le descripteur ne peut pas écrire le matériel de clé lors de la sérialisation, car le HSM n’expose pas les éléments sous forme de texte en clair. Au lieu de cela, le descripteur peut écrire la version encapsulée de la clé (si le HSM autorise l’exportation de cette manière) ou l’identificateur unique du HSM pour la clé.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

L’interface **IAuthenticatedEncryptorDescriptorDeserializer** représente un type qui sait comment désérialiser une instance IAuthenticatedEncryptorDescriptor à partir d’un XElement. Il expose une méthode unique :

* ImportFromXml (élément XElement) : IAuthenticatedEncryptorDescriptor

La méthode ImportFromXml prend le XElement qui a été retourné par [IAuthenticatedEncryptorDescriptor. ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) et crée un équivalent du IAuthenticatedEncryptorDescriptor d’origine.

Les types qui implémentent IAuthenticatedEncryptorDescriptorDeserializer doivent avoir l’un des deux constructeurs publics suivants :

* . ctor (IServiceProvider)

* . ctor ()

> [!NOTE]
> Le IServiceProvider passé au constructeur peut avoir la valeur null.

## <a name="the-top-level-factory"></a>La fabrique de niveau supérieur

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

La classe **AlgorithmConfiguration** représente un type qui sait comment créer des instances [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Il expose une seule API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Imaginez AlgorithmConfiguration comme fabrique de niveau supérieur. La configuration sert de modèle. Elle encapsule les informations algorithmiques (par exemple, cette configuration génère des descripteurs avec une clé principale AES-128-GCM), mais elle n’est pas encore associée à une clé spécifique.

Quand CreateNewDescriptor est appelé, le matériel de clé actualisé est créé uniquement pour cet appel, et un nouveau IAuthenticatedEncryptorDescriptor est généré, ce qui encapsule ce matériel de clé et les informations algorithmiques requises pour consommer le matériel. Le matériel de clé peut être créé dans le logiciel (et conservé en mémoire), il peut être créé et conservé dans un HSM, et ainsi de suite. Le point essentiel est que les deux appels à CreateNewDescriptor ne doivent jamais créer des instances IAuthenticatedEncryptorDescriptor équivalentes.

Le type AlgorithmConfiguration sert de point d’entrée pour les routines de création de clés telles que le [déroulement automatique de clés](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Pour modifier l’implémentation pour toutes les futures clés, définissez la propriété AuthenticatedEncryptorConfiguration dans KeyManagementOptions.

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

L’interface **IAuthenticatedEncryptorConfiguration** représente un type qui sait comment créer des instances [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) . Il expose une seule API.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Imaginez IAuthenticatedEncryptorConfiguration comme fabrique de niveau supérieur. La configuration sert de modèle. Elle encapsule les informations algorithmiques (par exemple, cette configuration génère des descripteurs avec une clé principale AES-128-GCM), mais elle n’est pas encore associée à une clé spécifique.

Quand CreateNewDescriptor est appelé, le matériel de clé actualisé est créé uniquement pour cet appel, et un nouveau IAuthenticatedEncryptorDescriptor est généré, ce qui encapsule ce matériel de clé et les informations algorithmiques requises pour consommer le matériel. Le matériel de clé peut être créé dans le logiciel (et conservé en mémoire), il peut être créé et conservé dans un HSM, et ainsi de suite. Le point essentiel est que les deux appels à CreateNewDescriptor ne doivent jamais créer des instances IAuthenticatedEncryptorDescriptor équivalentes.

Le type IAuthenticatedEncryptorConfiguration sert de point d’entrée pour les routines de création de clés telles que le [déroulement automatique de clés](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Pour modifier l’implémentation pour toutes les futures clés, inscrivez un IAuthenticatedEncryptorConfiguration Singleton dans le conteneur de service.

---
