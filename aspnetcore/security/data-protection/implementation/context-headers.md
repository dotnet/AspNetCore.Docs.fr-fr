---
title: En-têtes de contexte dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation des en-têtes de contexte de protection des données ASP.NET Core.
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051718"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="1cabb-103">En-têtes de contexte dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1cabb-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="1cabb-104">Arrière-plan et théorie</span><span class="sxs-lookup"><span data-stu-id="1cabb-104">Background and theory</span></span>

<span data-ttu-id="1cabb-105">Dans le système de protection des données, une « clé » désigne un objet qui peut fournir des services de chiffrement authentifiés.</span><span class="sxs-lookup"><span data-stu-id="1cabb-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="1cabb-106">Chaque clé est identifiée par un ID unique (GUID) et elle contient des informations algorithmiques et des documents Entropic.</span><span class="sxs-lookup"><span data-stu-id="1cabb-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="1cabb-107">Il est prévu que chaque clé porte une entropie unique, mais le système ne peut pas le faire, et nous devons également tenir compte des développeurs qui peuvent changer manuellement l’anneau de clé en modifiant les informations algorithmiques d’une clé existante dans l’anneau de clé.</span><span class="sxs-lookup"><span data-stu-id="1cabb-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="1cabb-108">Pour répondre à nos exigences de sécurité, le système de protection des données présente un concept d' [agilité cryptographique](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), qui permet d’utiliser en toute sécurité une valeur Entropic unique sur plusieurs algorithmes de chiffrement.</span><span class="sxs-lookup"><span data-stu-id="1cabb-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="1cabb-109">La plupart des systèmes qui prennent en charge l’agilité de chiffrement le font en incluant des informations d’identification sur l’algorithme à l’intérieur de la charge utile.</span><span class="sxs-lookup"><span data-stu-id="1cabb-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="1cabb-110">L’OID de l’algorithme est généralement un bon candidat pour cela.</span><span class="sxs-lookup"><span data-stu-id="1cabb-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="1cabb-111">Toutefois, l’un des problèmes que nous avons rencontré est qu’il existe plusieurs façons de spécifier le même algorithme : « AES » (CNG) et les classes AES, AesManaged, AesCryptoServiceProvider, AesCng et RijndaelManaged (données spécifiques) managées sont toutes identiques, et nous devons conserver un mappage de tous ces éléments sur l’OID correct.</span><span class="sxs-lookup"><span data-stu-id="1cabb-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="1cabb-112">Si un développeur souhaitait fournir un algorithme personnalisé (ou même une autre implémentation d’AES !), il aurait à nous dire son OID.</span><span class="sxs-lookup"><span data-stu-id="1cabb-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="1cabb-113">Cette étape d’enregistrement supplémentaire rend la configuration du système particulièrement pénible.</span><span class="sxs-lookup"><span data-stu-id="1cabb-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="1cabb-114">En arrière-plan, nous avons décidé que nous approchions le problème de la mauvaise direction.</span><span class="sxs-lookup"><span data-stu-id="1cabb-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="1cabb-115">Un OID vous indique l’algorithme, mais ce n’est pas vraiment une préoccupation.</span><span class="sxs-lookup"><span data-stu-id="1cabb-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="1cabb-116">Si nous avons besoin d’utiliser une seule valeur Entropic en toute sécurité dans deux algorithmes différents, il n’est pas nécessaire pour nous de savoir ce que sont les algorithmes.</span><span class="sxs-lookup"><span data-stu-id="1cabb-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="1cabb-117">Ce dont nous sommes particulièrement soucis, c’est la façon dont ils se comportent.</span><span class="sxs-lookup"><span data-stu-id="1cabb-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="1cabb-118">Tout autre algorithme de chiffrement par bloc symétrique est également une puissante permutation aléatoire : corriger les entrées (clé, mode chaînage, IV, texte brut) et la sortie de texte chiffré avec une probabilité écrasante est distincte de tout autre algorithme de chiffrement par bloc symétrique en raison des mêmes entrées.</span><span class="sxs-lookup"><span data-stu-id="1cabb-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="1cabb-119">De même, toute fonction de hachage à clé correcte est également une fonction de chiffrement aléatoire forte (PRF), et étant donné un jeu de données d’entrée fixe, sa sortie est très lourdement différente de toute autre fonction de hachage à clé.</span><span class="sxs-lookup"><span data-stu-id="1cabb-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="1cabb-120">Nous utilisons ce concept de Strong mot et PRFs pour créer un en-tête de contexte.</span><span class="sxs-lookup"><span data-stu-id="1cabb-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="1cabb-121">Cet en-tête de contexte agit essentiellement comme une empreinte numérique stable sur les algorithmes utilisés pour une opération donnée, et fournit l’agilité de chiffrement requise par le système de protection des données.</span><span class="sxs-lookup"><span data-stu-id="1cabb-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="1cabb-122">Cet en-tête est reproductible et est utilisé ultérieurement dans le cadre du [processus de dérivation de sous-clés](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="1cabb-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="1cabb-123">Il existe deux façons différentes de créer l’en-tête de contexte en fonction des modes de fonctionnement des algorithmes sous-jacents.</span><span class="sxs-lookup"><span data-stu-id="1cabb-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="1cabb-124">Chiffrement en mode CBC + authentification HMAC</span><span class="sxs-lookup"><span data-stu-id="1cabb-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="1cabb-125">L’en-tête de contexte est constitué des composants suivants :</span><span class="sxs-lookup"><span data-stu-id="1cabb-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="1cabb-126">[16 bits] La valeur 00 00, qui est un marqueur signifiant « chiffrement CBC + authentification HMAC ».</span><span class="sxs-lookup"><span data-stu-id="1cabb-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="1cabb-127">[32 bits] Longueur de la clé (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.</span><span class="sxs-lookup"><span data-stu-id="1cabb-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="1cabb-128">[32 bits] Taille de bloc (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.</span><span class="sxs-lookup"><span data-stu-id="1cabb-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="1cabb-129">[32 bits] Longueur de la clé (en octets, Big-endian) de l’algorithme HMAC.</span><span class="sxs-lookup"><span data-stu-id="1cabb-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="1cabb-130">(Actuellement, la taille de clé correspond toujours à la taille du condensé.)</span><span class="sxs-lookup"><span data-stu-id="1cabb-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="1cabb-131">[32 bits] Taille du condensé (en octets, Big-endian) de l’algorithme HMAC.</span><span class="sxs-lookup"><span data-stu-id="1cabb-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="1cabb-132">`EncCBC(K_E, IV, "")`, qui est la sortie de l’algorithme de chiffrement par bloc symétrique en fonction d’une entrée de chaîne vide et où IV est un vecteur tout zéro.</span><span class="sxs-lookup"><span data-stu-id="1cabb-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="1cabb-133">La construction de `K_E` est décrite ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="1cabb-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="1cabb-134">`MAC(K_H, "")`, qui est la sortie de l’algorithme HMAC en fonction d’une entrée de chaîne vide.</span><span class="sxs-lookup"><span data-stu-id="1cabb-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="1cabb-135">La construction de `K_H` est décrite ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="1cabb-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="1cabb-136">Idéalement, nous pourrions passer tous les vecteurs nuls pour `K_E` et `K_H` .</span><span class="sxs-lookup"><span data-stu-id="1cabb-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="1cabb-137">Toutefois, nous souhaitons éviter la situation dans laquelle l’algorithme sous-jacent vérifie l’existence de clés faibles avant d’effectuer des opérations (notamment DES et 3DES), ce qui empêche l’utilisation d’un modèle simple ou reproductible comme un vecteur tout zéro.</span><span class="sxs-lookup"><span data-stu-id="1cabb-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="1cabb-138">Au lieu de cela, nous utilisons le KDF NIST SP800-108 en mode compteur (voir [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) avec une clé, une étiquette et un contexte de longueur nulle, et HMACSHA512 comme le PRF sous-jacent.</span><span class="sxs-lookup"><span data-stu-id="1cabb-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="1cabb-139">Nous dérivez `| K_E | + | K_H |` des octets de sortie, puis décomposons le résultat en `K_E` et `K_H` eux-mêmes.</span><span class="sxs-lookup"><span data-stu-id="1cabb-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="1cabb-140">Mathématiquement, ce qui est représenté comme suit.</span><span class="sxs-lookup"><span data-stu-id="1cabb-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="1cabb-141">Exemple : AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="1cabb-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="1cabb-142">Par exemple, considérez le cas où l’algorithme de chiffrement par bloc symétrique est AES-192-CBC et que l’algorithme de validation est HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="1cabb-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="1cabb-143">Le système génère l’en-tête de contexte à l’aide des étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="1cabb-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="1cabb-144">Tout d’abord, indiquez `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , où `| K_E | = 192 bits` et `| K_H | = 256 bits` par les algorithmes spécifiés.</span><span class="sxs-lookup"><span data-stu-id="1cabb-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="1cabb-145">Cela amène à `K_E = 5BB6..21DD` et `K_H = A04A..00A9` dans l’exemple ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="1cabb-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="1cabb-146">Ensuite, calculez `Enc_CBC (K_E, IV, "")` pour AES-192-CBC donné `IV = 0*` et `K_E` comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1cabb-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="1cabb-147">Ensuite, calculez `MAC(K_H, "")` pour HMACSHA256 donné `K_H` comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1cabb-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="1cabb-148">Cela génère l’en-tête de contexte complet ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="1cabb-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="1cabb-149">Cet en-tête de contexte est l’empreinte numérique de la paire d’algorithmes de chiffrement authentifiée (AES-192-CBC Encryption + HMACSHA256 validation).</span><span class="sxs-lookup"><span data-stu-id="1cabb-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="1cabb-150">Les composants, comme décrit [ci-dessus](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , sont les suivants :</span><span class="sxs-lookup"><span data-stu-id="1cabb-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="1cabb-151">le marqueur `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="1cabb-152">longueur de la clé de chiffrement par bloc `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="1cabb-153">taille du bloc de chiffrement par bloc `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="1cabb-154">longueur de clé HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="1cabb-155">taille du condensé HMAC `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="1cabb-156">sortie de chiffrement par bloc `(F4 74 - DB 6F)` et</span><span class="sxs-lookup"><span data-stu-id="1cabb-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="1cabb-157">sortie du PRF HMAC `(D4 79 - end)` .</span><span class="sxs-lookup"><span data-stu-id="1cabb-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="1cabb-158">L’en-tête du contexte d’authentification du chiffrement en mode CBC + HMAC est créé de la même façon que les implémentations des algorithmes soient fournies par le CNG Windows ou par les types SymmetricAlgorithm et KeyedHashAlgorithm managés.</span><span class="sxs-lookup"><span data-stu-id="1cabb-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="1cabb-159">Cela permet aux applications exécutées sur différents systèmes d’exploitation de produire de manière fiable le même en-tête de contexte même si les implémentations des algorithmes diffèrent entre les systèmes d’exploitation.</span><span class="sxs-lookup"><span data-stu-id="1cabb-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="1cabb-160">(En pratique, KeyedHashAlgorithm n’a pas besoin d’être un HMAC approprié.</span><span class="sxs-lookup"><span data-stu-id="1cabb-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="1cabb-161">Il peut s’agir de n’importe quel type d’algorithme de hachage à clé.)</span><span class="sxs-lookup"><span data-stu-id="1cabb-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="1cabb-162">Exemple : 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="1cabb-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="1cabb-163">Tout d’abord, indiquez `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , où `| K_E | = 192 bits` et `| K_H | = 160 bits` par les algorithmes spécifiés.</span><span class="sxs-lookup"><span data-stu-id="1cabb-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="1cabb-164">Cela amène à `K_E = A219..E2BB` et `K_H = DC4A..B464` dans l’exemple ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="1cabb-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="1cabb-165">Ensuite, calculez `Enc_CBC (K_E, IV, "")` pour 3DES-192-CBC donné `IV = 0*` et `K_E` comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1cabb-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="1cabb-166">Ensuite, calculez `MAC(K_H, "")` pour HMACSHA1 `K_H` comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1cabb-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="1cabb-167">Cela génère l’en-tête de contexte complet qui est une empreinte de la paire d’algorithmes de chiffrement authentifiés (3DES-192-CBC Encryption + HMACSHA1 validation), comme indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="1cabb-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="1cabb-168">Les composants s’interrompent comme suit :</span><span class="sxs-lookup"><span data-stu-id="1cabb-168">The components break down as follows:</span></span>

* <span data-ttu-id="1cabb-169">le marqueur `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="1cabb-170">longueur de la clé de chiffrement par bloc `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="1cabb-171">taille du bloc de chiffrement par bloc `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="1cabb-172">longueur de clé HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="1cabb-173">taille du condensé HMAC `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="1cabb-174">sortie de chiffrement par bloc `(AB B1 - E1 0E)` et</span><span class="sxs-lookup"><span data-stu-id="1cabb-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="1cabb-175">sortie du PRF HMAC `(76 EB - end)` .</span><span class="sxs-lookup"><span data-stu-id="1cabb-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="1cabb-176">Chiffrement + authentification du mode Galois/Counter</span><span class="sxs-lookup"><span data-stu-id="1cabb-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="1cabb-177">L’en-tête de contexte est constitué des composants suivants :</span><span class="sxs-lookup"><span data-stu-id="1cabb-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="1cabb-178">[16 bits] La valeur 00 01, qui est un marqueur signifiant « GCM Encryption + Authentication ».</span><span class="sxs-lookup"><span data-stu-id="1cabb-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="1cabb-179">[32 bits] Longueur de la clé (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.</span><span class="sxs-lookup"><span data-stu-id="1cabb-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="1cabb-180">[32 bits] Taille de la valeur à usage unique (en octets, Big-endian) utilisée pendant les opérations de chiffrement authentifié.</span><span class="sxs-lookup"><span data-stu-id="1cabb-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="1cabb-181">(Pour notre système, cette valeur est fixe au format nonce = 96 bits.)</span><span class="sxs-lookup"><span data-stu-id="1cabb-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="1cabb-182">[32 bits] Taille de bloc (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.</span><span class="sxs-lookup"><span data-stu-id="1cabb-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="1cabb-183">(Pour GCM, cette valeur est fixe à la taille de bloc = 128 bits.)</span><span class="sxs-lookup"><span data-stu-id="1cabb-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="1cabb-184">[32 bits] Taille de la balise d’authentification (en octets, Big-endian) produite par la fonction de chiffrement authentifié.</span><span class="sxs-lookup"><span data-stu-id="1cabb-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="1cabb-185">(Pour notre système, ce problème est résolu au niveau de la balise Size = 128 bits.)</span><span class="sxs-lookup"><span data-stu-id="1cabb-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="1cabb-186">[128 bits] La balise de `Enc_GCM (K_E, nonce, "")` , qui est la sortie de l’algorithme de chiffrement par bloc symétrique en fonction d’une entrée de chaîne vide et où nonce est un vecteur tout-zéro 96 bits.</span><span class="sxs-lookup"><span data-stu-id="1cabb-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="1cabb-187">`K_E` est dérivé à l’aide du même mécanisme que dans le scénario d’authentification CBC Encryption + HMAC.</span><span class="sxs-lookup"><span data-stu-id="1cabb-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="1cabb-188">Toutefois, étant donné qu’il n’y a aucun `K_H` dans la lecture ici, nous avons essentiellement `| K_H | = 0` , et l’algorithme est réduit au formulaire ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="1cabb-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="1cabb-189">Exemple : AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="1cabb-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="1cabb-190">Tout d’abord, `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` où `| K_E | = 256 bits` .</span><span class="sxs-lookup"><span data-stu-id="1cabb-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="1cabb-191">Ensuite, calculez la balise d’authentification de `Enc_GCM (K_E, nonce, "")` pour AES-256-GCM donné `nonce = 096` et `K_E` comme indiqué ci-dessus.</span><span class="sxs-lookup"><span data-stu-id="1cabb-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="1cabb-192">Cela génère l’en-tête de contexte complet ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="1cabb-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="1cabb-193">Les composants s’interrompent comme suit :</span><span class="sxs-lookup"><span data-stu-id="1cabb-193">The components break down as follows:</span></span>

* <span data-ttu-id="1cabb-194">le marqueur `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="1cabb-195">longueur de la clé de chiffrement par bloc `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="1cabb-196">la taille de la valeur à usage unique `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="1cabb-197">taille du bloc de chiffrement par bloc `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="1cabb-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="1cabb-198">la taille de la balise d’authentification `(00 00 00 10)` et</span><span class="sxs-lookup"><span data-stu-id="1cabb-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="1cabb-199">balise d’authentification de l’exécution du chiffrement par blocs `(E7 DC - end)` .</span><span class="sxs-lookup"><span data-stu-id="1cabb-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
