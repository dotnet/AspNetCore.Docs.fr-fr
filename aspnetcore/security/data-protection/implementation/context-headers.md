---
title: En-têtes de contexte dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation des en-têtes de contexte de protection des données ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051718"
---
# <a name="context-headers-in-aspnet-core"></a>En-têtes de contexte dans ASP.NET Core

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Arrière-plan et théorie

Dans le système de protection des données, une « clé » désigne un objet qui peut fournir des services de chiffrement authentifiés. Chaque clé est identifiée par un ID unique (GUID) et elle contient des informations algorithmiques et des documents Entropic. Il est prévu que chaque clé porte une entropie unique, mais le système ne peut pas le faire, et nous devons également tenir compte des développeurs qui peuvent changer manuellement l’anneau de clé en modifiant les informations algorithmiques d’une clé existante dans l’anneau de clé. Pour répondre à nos exigences de sécurité, le système de protection des données présente un concept d' [agilité cryptographique](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), qui permet d’utiliser en toute sécurité une valeur Entropic unique sur plusieurs algorithmes de chiffrement.

La plupart des systèmes qui prennent en charge l’agilité de chiffrement le font en incluant des informations d’identification sur l’algorithme à l’intérieur de la charge utile. L’OID de l’algorithme est généralement un bon candidat pour cela. Toutefois, l’un des problèmes que nous avons rencontré est qu’il existe plusieurs façons de spécifier le même algorithme : « AES » (CNG) et les classes AES, AesManaged, AesCryptoServiceProvider, AesCng et RijndaelManaged (données spécifiques) managées sont toutes identiques, et nous devons conserver un mappage de tous ces éléments sur l’OID correct. Si un développeur souhaitait fournir un algorithme personnalisé (ou même une autre implémentation d’AES !), il aurait à nous dire son OID. Cette étape d’enregistrement supplémentaire rend la configuration du système particulièrement pénible.

En arrière-plan, nous avons décidé que nous approchions le problème de la mauvaise direction. Un OID vous indique l’algorithme, mais ce n’est pas vraiment une préoccupation. Si nous avons besoin d’utiliser une seule valeur Entropic en toute sécurité dans deux algorithmes différents, il n’est pas nécessaire pour nous de savoir ce que sont les algorithmes. Ce dont nous sommes particulièrement soucis, c’est la façon dont ils se comportent. Tout autre algorithme de chiffrement par bloc symétrique est également une puissante permutation aléatoire : corriger les entrées (clé, mode chaînage, IV, texte brut) et la sortie de texte chiffré avec une probabilité écrasante est distincte de tout autre algorithme de chiffrement par bloc symétrique en raison des mêmes entrées. De même, toute fonction de hachage à clé correcte est également une fonction de chiffrement aléatoire forte (PRF), et étant donné un jeu de données d’entrée fixe, sa sortie est très lourdement différente de toute autre fonction de hachage à clé.

Nous utilisons ce concept de Strong mot et PRFs pour créer un en-tête de contexte. Cet en-tête de contexte agit essentiellement comme une empreinte numérique stable sur les algorithmes utilisés pour une opération donnée, et fournit l’agilité de chiffrement requise par le système de protection des données. Cet en-tête est reproductible et est utilisé ultérieurement dans le cadre du [processus de dérivation de sous-clés](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Il existe deux façons différentes de créer l’en-tête de contexte en fonction des modes de fonctionnement des algorithmes sous-jacents.

## <a name="cbc-mode-encryption--hmac-authentication"></a>Chiffrement en mode CBC + authentification HMAC

<a name="data-protection-implementation-context-headers-cbc-components"></a>

L’en-tête de contexte est constitué des composants suivants :

* [16 bits] La valeur 00 00, qui est un marqueur signifiant « chiffrement CBC + authentification HMAC ».

* [32 bits] Longueur de la clé (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.

* [32 bits] Taille de bloc (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.

* [32 bits] Longueur de la clé (en octets, Big-endian) de l’algorithme HMAC. (Actuellement, la taille de clé correspond toujours à la taille du condensé.)

* [32 bits] Taille du condensé (en octets, Big-endian) de l’algorithme HMAC.

* `EncCBC(K_E, IV, "")`, qui est la sortie de l’algorithme de chiffrement par bloc symétrique en fonction d’une entrée de chaîne vide et où IV est un vecteur tout zéro. La construction de `K_E` est décrite ci-dessous.

* `MAC(K_H, "")`, qui est la sortie de l’algorithme HMAC en fonction d’une entrée de chaîne vide. La construction de `K_H` est décrite ci-dessous.

Idéalement, nous pourrions passer tous les vecteurs nuls pour `K_E` et `K_H` . Toutefois, nous souhaitons éviter la situation dans laquelle l’algorithme sous-jacent vérifie l’existence de clés faibles avant d’effectuer des opérations (notamment DES et 3DES), ce qui empêche l’utilisation d’un modèle simple ou reproductible comme un vecteur tout zéro.

Au lieu de cela, nous utilisons le KDF NIST SP800-108 en mode compteur (voir [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) avec une clé, une étiquette et un contexte de longueur nulle, et HMACSHA512 comme le PRF sous-jacent. Nous dérivez `| K_E | + | K_H |` des octets de sortie, puis décomposons le résultat en `K_E` et `K_H` eux-mêmes. Mathématiquement, ce qui est représenté comme suit.

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a>Exemple : AES-192-CBC + HMACSHA256

Par exemple, considérez le cas où l’algorithme de chiffrement par bloc symétrique est AES-192-CBC et que l’algorithme de validation est HMACSHA256. Le système génère l’en-tête de contexte à l’aide des étapes suivantes.

Tout d’abord, indiquez `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , où `| K_E | = 192 bits` et `| K_H | = 256 bits` par les algorithmes spécifiés. Cela amène à `K_E = 5BB6..21DD` et `K_H = A04A..00A9` dans l’exemple ci-dessous :

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

Ensuite, calculez `Enc_CBC (K_E, IV, "")` pour AES-192-CBC donné `IV = 0*` et `K_E` comme indiqué ci-dessus.

`result := F474B1872B3B53E4721DE19C0841DB6F`

Ensuite, calculez `MAC(K_H, "")` pour HMACSHA256 donné `K_H` comme indiqué ci-dessus.

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

Cela génère l’en-tête de contexte complet ci-dessous :

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Cet en-tête de contexte est l’empreinte numérique de la paire d’algorithmes de chiffrement authentifiée (AES-192-CBC Encryption + HMACSHA256 validation). Les composants, comme décrit [ci-dessus](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) , sont les suivants :

* le marqueur `(00 00)`

* longueur de la clé de chiffrement par bloc `(00 00 00 18)`

* taille du bloc de chiffrement par bloc `(00 00 00 10)`

* longueur de clé HMAC `(00 00 00 20)`

* taille du condensé HMAC `(00 00 00 20)`

* sortie de chiffrement par bloc `(F4 74 - DB 6F)` et

* sortie du PRF HMAC `(D4 79 - end)` .

> [!NOTE]
> L’en-tête du contexte d’authentification du chiffrement en mode CBC + HMAC est créé de la même façon que les implémentations des algorithmes soient fournies par le CNG Windows ou par les types SymmetricAlgorithm et KeyedHashAlgorithm managés. Cela permet aux applications exécutées sur différents systèmes d’exploitation de produire de manière fiable le même en-tête de contexte même si les implémentations des algorithmes diffèrent entre les systèmes d’exploitation. (En pratique, KeyedHashAlgorithm n’a pas besoin d’être un HMAC approprié. Il peut s’agir de n’importe quel type d’algorithme de hachage à clé.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Exemple : 3DES-192-CBC + HMACSHA1

Tout d’abord, indiquez `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , où `| K_E | = 192 bits` et `| K_H | = 160 bits` par les algorithmes spécifiés. Cela amène à `K_E = A219..E2BB` et `K_H = DC4A..B464` dans l’exemple ci-dessous :

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

Ensuite, calculez `Enc_CBC (K_E, IV, "")` pour 3DES-192-CBC donné `IV = 0*` et `K_E` comme indiqué ci-dessus.

`result := ABB100F81E53E10E`

Ensuite, calculez `MAC(K_H, "")` pour HMACSHA1 `K_H` comme indiqué ci-dessus.

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

Cela génère l’en-tête de contexte complet qui est une empreinte de la paire d’algorithmes de chiffrement authentifiés (3DES-192-CBC Encryption + HMACSHA1 validation), comme indiqué ci-dessous :

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Les composants s’interrompent comme suit :

* le marqueur `(00 00)`

* longueur de la clé de chiffrement par bloc `(00 00 00 18)`

* taille du bloc de chiffrement par bloc `(00 00 00 08)`

* longueur de clé HMAC `(00 00 00 14)`

* taille du condensé HMAC `(00 00 00 14)`

* sortie de chiffrement par bloc `(AB B1 - E1 0E)` et

* sortie du PRF HMAC `(76 EB - end)` .

## <a name="galoiscounter-mode-encryption--authentication"></a>Chiffrement + authentification du mode Galois/Counter

L’en-tête de contexte est constitué des composants suivants :

* [16 bits] La valeur 00 01, qui est un marqueur signifiant « GCM Encryption + Authentication ».

* [32 bits] Longueur de la clé (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique.

* [32 bits] Taille de la valeur à usage unique (en octets, Big-endian) utilisée pendant les opérations de chiffrement authentifié. (Pour notre système, cette valeur est fixe au format nonce = 96 bits.)

* [32 bits] Taille de bloc (en octets, Big-endian) de l’algorithme de chiffrement par bloc symétrique. (Pour GCM, cette valeur est fixe à la taille de bloc = 128 bits.)

* [32 bits] Taille de la balise d’authentification (en octets, Big-endian) produite par la fonction de chiffrement authentifié. (Pour notre système, ce problème est résolu au niveau de la balise Size = 128 bits.)

* [128 bits] La balise de `Enc_GCM (K_E, nonce, "")` , qui est la sortie de l’algorithme de chiffrement par bloc symétrique en fonction d’une entrée de chaîne vide et où nonce est un vecteur tout-zéro 96 bits.

`K_E` est dérivé à l’aide du même mécanisme que dans le scénario d’authentification CBC Encryption + HMAC. Toutefois, étant donné qu’il n’y a aucun `K_H` dans la lecture ici, nous avons essentiellement `| K_H | = 0` , et l’algorithme est réduit au formulaire ci-dessous.

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a>Exemple : AES-256-GCM

Tout d’abord, `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` où `| K_E | = 256 bits` .

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

Ensuite, calculez la balise d’authentification de `Enc_GCM (K_E, nonce, "")` pour AES-256-GCM donné `nonce = 096` et `K_E` comme indiqué ci-dessus.

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

Cela génère l’en-tête de contexte complet ci-dessous :

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Les composants s’interrompent comme suit :

* le marqueur `(00 01)`

* longueur de la clé de chiffrement par bloc `(00 00 00 20)`

* la taille de la valeur à usage unique `(00 00 00 0C)`

* taille du bloc de chiffrement par bloc `(00 00 00 10)`

* la taille de la balise d’authentification `(00 00 00 10)` et

* balise d’authentification de l’exécution du chiffrement par blocs `(E7 DC - end)` .
