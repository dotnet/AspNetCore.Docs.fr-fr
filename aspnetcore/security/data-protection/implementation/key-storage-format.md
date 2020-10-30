---
title: Format de stockage des clés dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation du format de stockage de la clé de protection des données ASP.NET Core.
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 4a8503964c98d1828dc9d02640a7621b370e679c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060142"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="25267-103">Format de stockage des clés dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="25267-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="25267-104">Les objets sont stockés au repos dans une représentation XML.</span><span class="sxs-lookup"><span data-stu-id="25267-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="25267-105">Le répertoire par défaut pour le stockage des clés est le suivant :</span><span class="sxs-lookup"><span data-stu-id="25267-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="25267-106">Windows : \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="25267-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="25267-107">macOS/Linux : *$Home/.AspNet/dataprotection-Keys*</span><span class="sxs-lookup"><span data-stu-id="25267-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="25267-108">Élément \<key></span><span class="sxs-lookup"><span data-stu-id="25267-108">The \<key> element</span></span>

<span data-ttu-id="25267-109">Les clés existent en tant qu’objets de niveau supérieur dans le dépôt de clé.</span><span class="sxs-lookup"><span data-stu-id="25267-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="25267-110">Les clés de Convention ont la clé de nom de fichier **-{GUID}. xml** , où {GUID} est l’ID de la clé.</span><span class="sxs-lookup"><span data-stu-id="25267-110">By convention keys have the filename **key-{guid}.xml** , where {guid} is the id of the key.</span></span> <span data-ttu-id="25267-111">Chaque fichier de ce type contient une clé unique.</span><span class="sxs-lookup"><span data-stu-id="25267-111">Each such file contains a single key.</span></span> <span data-ttu-id="25267-112">Le format du fichier est le suivant.</span><span class="sxs-lookup"><span data-stu-id="25267-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="25267-113">L' \<key> élément contient les attributs et les éléments enfants suivants :</span><span class="sxs-lookup"><span data-stu-id="25267-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="25267-114">ID de la clé. Cette valeur est traitée comme faisant autorité ; le nom de fichier est simplement un nicety pour la lisibilité humaine.</span><span class="sxs-lookup"><span data-stu-id="25267-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="25267-115">Version de l' \<key> élément, actuellement fixée à 1.</span><span class="sxs-lookup"><span data-stu-id="25267-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="25267-116">Dates de création, d’activation et d’expiration de la clé.</span><span class="sxs-lookup"><span data-stu-id="25267-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="25267-117">\<descriptor>Élément qui contient des informations sur l’implémentation de chiffrement authentifié contenue dans cette clé.</span><span class="sxs-lookup"><span data-stu-id="25267-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="25267-118">Dans l’exemple ci-dessus, l’ID de la clé est {80732141-ec8f-4B80-af9c-c4d2d1ff8901}, il a été créé et activé le 19 mars 2015, et sa durée de vie est de 90 jours.</span><span class="sxs-lookup"><span data-stu-id="25267-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="25267-119">(Parfois, la date d’activation peut être légèrement antérieure à la date de création, comme dans cet exemple.</span><span class="sxs-lookup"><span data-stu-id="25267-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="25267-120">Cela est dû à un Nil dans la manière dont les API fonctionnent et est sans conséquence dans la pratique.)</span><span class="sxs-lookup"><span data-stu-id="25267-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="25267-121">Élément \<descriptor></span><span class="sxs-lookup"><span data-stu-id="25267-121">The \<descriptor> element</span></span>

<span data-ttu-id="25267-122">L' \<descriptor> élément externe contient un attribut deserializerType, qui est le nom qualifié d’assembly d’un type qui implémente IAuthenticatedEncryptorDescriptorDeserializer.</span><span class="sxs-lookup"><span data-stu-id="25267-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="25267-123">Ce type est responsable de la lecture de l' \<descriptor> élément interne et de l’analyse des informations contenues dans.</span><span class="sxs-lookup"><span data-stu-id="25267-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="25267-124">Le format particulier de l' \<descriptor> élément dépend de l’implémentation du chiffreur authentifié encapsulé par la clé, et chaque type de désérialiseur attend un format légèrement différent pour ce.</span><span class="sxs-lookup"><span data-stu-id="25267-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="25267-125">En général, cependant, cet élément contient des informations algorithmiques (noms, types, OID ou similaires) et un matériel de clé secrète.</span><span class="sxs-lookup"><span data-stu-id="25267-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="25267-126">Dans l’exemple ci-dessus, le descripteur spécifie que cette clé encapsule AES-256-CBC Encryption + HMACSHA256 validation.</span><span class="sxs-lookup"><span data-stu-id="25267-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="25267-127">Élément \<encryptedSecret></span><span class="sxs-lookup"><span data-stu-id="25267-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="25267-128">Un élément **&lt; encryptedSecret &gt;** qui contient la forme chiffrée du matériel de clé secrète peut être présent si le [chiffrement des secrets au repos est activé](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="25267-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="25267-129">L’attribut `decryptorType` est le nom qualifié d’assembly d’un type qui implémente [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="25267-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="25267-130">Ce type est chargé de lire l’élément **&lt; EncryptedKey &gt;** interne et de le déchiffrer pour récupérer le texte en clair d’origine.</span><span class="sxs-lookup"><span data-stu-id="25267-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="25267-131">Comme avec `<descriptor>` , le format particulier de l' `<encryptedSecret>` élément dépend du mécanisme de chiffrement au repos en cours d’utilisation.</span><span class="sxs-lookup"><span data-stu-id="25267-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="25267-132">Dans l’exemple ci-dessus, la clé principale est chiffrée à l’aide de Windows DPAPI pour le commentaire.</span><span class="sxs-lookup"><span data-stu-id="25267-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="25267-133">Élément \<revocation></span><span class="sxs-lookup"><span data-stu-id="25267-133">The \<revocation> element</span></span>

<span data-ttu-id="25267-134">Les révocations existent en tant qu’objets de niveau supérieur dans le dépôt de clé.</span><span class="sxs-lookup"><span data-stu-id="25267-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="25267-135">Par Convention, les révocations de nom de fichier **-{timestamp}. xml** (pour révoquer toutes les clés avant une date spécifique) ou la **révocation-{GUID}. xml** (pour révoquer une clé spécifique).</span><span class="sxs-lookup"><span data-stu-id="25267-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="25267-136">Chaque fichier contient un \<revocation> élément unique.</span><span class="sxs-lookup"><span data-stu-id="25267-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="25267-137">Pour les révocations de clés individuelles, le contenu du fichier se présente comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="25267-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="25267-138">Dans ce cas, seule la clé spécifiée est révoquée.</span><span class="sxs-lookup"><span data-stu-id="25267-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="25267-139">Toutefois, si l’ID de clé est « \* », comme dans l’exemple ci-dessous, toutes les clés dont la date de création est antérieure à la date de révocation spécifiée sont révoquées.</span><span class="sxs-lookup"><span data-stu-id="25267-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="25267-140">L' \<reason> élément n’est jamais lu par le système.</span><span class="sxs-lookup"><span data-stu-id="25267-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="25267-141">Il s’agit simplement d’un emplacement pratique pour stocker une raison explicite de révocation.</span><span class="sxs-lookup"><span data-stu-id="25267-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
