---
title: Extensibilité de la gestion des clés dans ASP.NET Core
author: rick-anderson
description: En savoir plus sur ASP.NET Core extensibilité de la gestion des clés de protection des données.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
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
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 08ec018238d18cd253e793267c5b146df8319485
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060766"
---
# <a name="key-management-extensibility-in-aspnet-core"></a>Extensibilité de la gestion des clés dans ASP.NET Core

Lisez la section [gestion des clés](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) avant de lire cette section, car elle explique certains concepts fondamentaux sous-jacents à ces API.

**Avertissement** : les types qui implémentent l’une des interfaces suivantes doivent être thread-safe pour plusieurs appelants.

## <a name="key"></a>Clé :

L' `IKey` interface est la représentation de base d’une clé dans chiffrement. Le terme clé est utilisé ici dans le sens abstrait, et non dans le sens littéral du « matériel de clé de chiffrement ». Une clé a les propriétés suivantes :

* Dates d’activation, de création et d’expiration

* État de révocation.

* Identificateur de clé (GUID)

::: moniker range=">= aspnetcore-2.0"

En outre, `IKey` expose une `CreateEncryptor` méthode qui peut être utilisée pour créer une instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) liée à cette clé.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

En outre, `IKey` expose une `CreateEncryptorInstance` méthode qui peut être utilisée pour créer une instance [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) liée à cette clé.

::: moniker-end

> [!NOTE]
> Il n’existe aucune API pour récupérer le matériel de chiffrement brut à partir d’une `IKey` instance.

## <a name="ikeymanager"></a>IKeyManager

L' `IKeyManager` interface représente un objet responsable du stockage, de la récupération et de la manipulation des clés générales. Il expose trois opérations de haut niveau :

* Créez une nouvelle clé et conservez-la dans le stockage.

* Récupération de toutes les clés à partir du stockage.

* Révoquez une ou plusieurs clés et conservez les informations de révocation dans le stockage.

>[!WARNING]
> L’écriture d’un `IKeyManager` est une tâche très avancée et la majorité des développeurs ne doivent pas la tenter. Au lieu de cela, la plupart des développeurs doivent tirer parti des fonctionnalités offertes par la classe [XmlKeyManager](#xmlkeymanager) .

## <a name="xmlkeymanager"></a>XmlKeyManager

Le `XmlKeyManager` type est l’implémentation concrète de `IKeyManager` . Il offre plusieurs fonctionnalités utiles, dont le dépôt de clés et le chiffrement des clés au repos. Les clés de ce système sont représentées en tant qu’éléments XML (en particulier, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` dépend de plusieurs autres composants dans le cadre de la réalisation de ses tâches :

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, qui dicte les algorithmes utilisés par les nouvelles clés.

* `IXmlRepository`, qui contrôle les emplacements où les clés sont conservées dans le stockage.

* `IXmlEncryptor` [facultatif], qui autorise le chiffrement des clés au repos.

* `IKeyEscrowSink` [facultatif], qui fournit des services de dépôt de clés.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, qui contrôle les emplacements où les clés sont conservées dans le stockage.

* `IXmlEncryptor` [facultatif], qui autorise le chiffrement des clés au repos.

* `IKeyEscrowSink` [facultatif], qui fournit des services de dépôt de clés.

::: moniker-end

Vous trouverez ci-dessous des diagrammes de haut niveau qui indiquent la façon dont ces composants sont connectés entre eux `XmlKeyManager` .

::: moniker range=">= aspnetcore-2.0"

![Création de la clé](key-management/_static/keycreation2.png)

*Création de clé/CreateNewKey*

Dans l’implémentation de `CreateNewKey` , le `AlgorithmConfiguration` composant est utilisé pour créer un unique `IAuthenticatedEncryptorDescriptor` , qui est ensuite sérialisé au format XML. Si un récepteur de dépôt de clé est présent, le XML brut (non chiffré) est fourni au récepteur pour le stockage à long terme. Le XML non chiffré est ensuite exécuté à l’aide d’un `IXmlEncryptor` (si nécessaire) pour générer le document XML chiffré. Ce document chiffré est conservé dans un stockage à long terme via le `IXmlRepository` . (Si aucun `IXmlEncryptor` n’est configuré, le document non chiffré est conservé dans le `IXmlRepository` .)

![Récupération de clé](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Création de la clé](key-management/_static/keycreation1.png)

*Création de clé/CreateNewKey*

Dans l’implémentation de `CreateNewKey` , le `IAuthenticatedEncryptorConfiguration` composant est utilisé pour créer un unique `IAuthenticatedEncryptorDescriptor` , qui est ensuite sérialisé au format XML. Si un récepteur de dépôt de clé est présent, le XML brut (non chiffré) est fourni au récepteur pour le stockage à long terme. Le XML non chiffré est ensuite exécuté à l’aide d’un `IXmlEncryptor` (si nécessaire) pour générer le document XML chiffré. Ce document chiffré est conservé dans un stockage à long terme via le `IXmlRepository` . (Si aucun `IXmlEncryptor` n’est configuré, le document non chiffré est conservé dans le `IXmlRepository` .)

![Récupération de clé](key-management/_static/keyretrieval1.png)

::: moniker-end

*Récupération de clé/GetAllKeys*

Dans l’implémentation de `GetAllKeys` , les documents XML représentant les clés et les révocations sont lus à partir du sous-jacent `IXmlRepository` . Si ces documents sont chiffrés, le système les déchiffre automatiquement. `XmlKeyManager` crée les `IAuthenticatedEncryptorDescriptorDeserializer` instances appropriées pour désérialiser les documents en `IAuthenticatedEncryptorDescriptor` instances, qui sont ensuite encapsulées dans des `IKey` instances individuelles. Cette collection d' `IKey` instances est retournée à l’appelant.

Vous trouverez plus d’informations sur les éléments XML particuliers dans le [document de format de stockage de clés](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

L' `IXmlRepository` interface représente un type qui peut conserver XML dans un magasin de stockage et récupérer du code XML. Il expose deux API :

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Les implémentations de `IXmlRepository` n’ont pas besoin d’analyser le XML qui les traverse. Ils doivent traiter les documents XML comme opaques et laisser les couches supérieures se soucier de la génération et de l’analyse des documents.

Il existe quatre types concrets intégrés qui implémentent `IXmlRepository` :

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage. AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Pour plus d’informations, consultez le document sur les [fournisseurs de stockage de clés](xref:security/data-protection/implementation/key-storage-providers) .

L’inscription d’un personnalisé `IXmlRepository` est appropriée lors de l’utilisation d’un autre magasin de stockage (par exemple, stockage table Azure).

Pour modifier le référentiel par défaut au niveau de l’application, inscrivez une `IXmlRepository` instance personnalisée :

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

L' `IXmlEncryptor` interface représente un type qui peut chiffrer un élément XML en texte brut. Il expose une seule API :

* Encrypt (XElement plaintextElement) : EncryptedXmlInfo

Si un sérialisé `IAuthenticatedEncryptorDescriptor` contient des éléments marqués comme « nécessite un chiffrement », `XmlKeyManager` exécute ces éléments par le biais de la `IXmlEncryptor` méthode de configurée `Encrypt` , et il conserve l’élément chiffré plutôt que l’élément de texte brut dans le `IXmlRepository` . La sortie de la `Encrypt` méthode est un `EncryptedXmlInfo` objet. Cet objet est un wrapper qui contient à la fois le chiffrement obtenu `XElement` et le type qui représente un `IXmlDecryptor` qui peut être utilisé pour déchiffrer l’élément correspondant.

Il existe quatre types concrets intégrés qui implémentent `IXmlEncryptor` :

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Pour plus d’informations, consultez le [document chiffrement à clé au repos](xref:security/data-protection/implementation/key-encryption-at-rest) .

Pour modifier le mécanisme par défaut de chiffrement par clé au repos à l’ensemble de l’application, inscrivez une `IXmlEncryptor` instance personnalisée :

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

L' `IXmlDecryptor` interface représente un type qui sait comment déchiffrer un `XElement` qui a été chiffré via un `IXmlEncryptor` . Il expose une seule API :

* Decrypt (XElement encryptedElement) : XElement

La `Decrypt` méthode annule le chiffrement effectué par `IXmlEncryptor.Encrypt` . En règle générale, chaque implémentation concrète aura `IXmlEncryptor` une implémentation concrète correspondante `IXmlDecryptor` .

Les types qui implémentent `IXmlDecryptor` doivent avoir l’un des deux constructeurs publics suivants :

* . ctor (IServiceProvider)
* . ctor ()

> [!NOTE]
> Le `IServiceProvider` passé au constructeur peut avoir la valeur null.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

L' `IKeyEscrowSink` interface représente un type qui peut effectuer un tiers de confiance d’informations sensibles. Rappelez-vous que les descripteurs sérialisés peuvent contenir des informations sensibles (telles que le matériel de chiffrement) et c’est ce qui a conduit à l’introduction du type [IXmlEncryptor](#ixmlencryptor) en premier lieu. Toutefois, les accidents se produisent et les sonneries de clé peuvent être supprimées ou endommagées.

L’interface du tiers de confiance fournit un hachage d’échappement d’urgence, permettant d’accéder au code XML sérialisé brut avant qu’il ne soit transformé par un [IXmlEncryptor](#ixmlencryptor)configuré. L’interface expose une seule API :

* Store (GUID keyId, élément XElement)

Il s’agit de l' `IKeyEscrowSink` implémentation pour gérer l’élément fourni de manière sécurisée et cohérente avec la stratégie d’entreprise. Une implémentation possible peut être que le récepteur de tiers de confiance chiffre l’élément XML à l’aide d’un certificat X. 509 d’entreprise connu dans lequel la clé privée du certificat a été déposée. le `CertificateXmlEncryptor` type peut vous aider. L' `IKeyEscrowSink` implémentation est également chargée de rendre l’élément fourni de manière appropriée.

Par défaut, aucun mécanisme tiers de confiance n’est activé, bien que les administrateurs de serveur puissent [le configurer globalement](xref:security/data-protection/configuration/machine-wide-policy). Elle peut également être configurée par programme à l’aide de la `IDataProtectionBuilder.AddKeyEscrowSink` méthode, comme illustré dans l’exemple ci-dessous. Les `AddKeyEscrowSink` surcharges de méthode reflètent `IServiceCollection.AddSingleton` les `IServiceCollection.AddInstance` surcharges et, car les `IKeyEscrowSink` instances sont destinées à être des singletons. Si plusieurs `IKeyEscrowSink` instances sont inscrites, chacune sera appelée pendant la génération de la clé, de sorte que les clés peuvent être déposées simultanément sur plusieurs mécanismes.

Il n’existe aucune API pour lire des documents à partir d’une `IKeyEscrowSink` instance. Cela est cohérent avec la théorie de la conception du mécanisme de dépôt : il est conçu pour rendre le matériel de clé accessible à une autorité de confiance, et puisque l’application n’est pas elle-même une autorité approuvée, elle ne doit pas avoir accès à ses propres documents de confiance.

L’exemple de code suivant illustre la création et l’enregistrement d’un `IKeyEscrowSink` dans lequel les clés sont déposées de sorte que seuls les membres de « CONTOSODomain admins » puissent les récupérer.

> [!NOTE]
> Pour exécuter cet exemple, vous devez être sur un ordinateur Windows 8/Windows Server 2012 joint à un domaine, et le contrôleur de domaine doit être Windows Server 2012 ou version ultérieure.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
