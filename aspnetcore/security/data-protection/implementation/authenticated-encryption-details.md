---
title: Détails du chiffrement authentifié dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation du chiffrement authentifié de la protection des données ASP.NET Core.
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 7978725534cdd3a5b425851f61b1c7ae3ada88df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051835"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>Détails du chiffrement authentifié dans ASP.NET Core

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Les appels à IDataProtector. Protect sont des opérations de chiffrement authentifiées. La méthode Protect offre à la fois la confidentialité et l’authenticité et est liée à la chaîne d’utilisation utilisée pour dériver cette instance IDataProtector particulière à partir de son IDataProtectionProvider racine.

IDataProtector. Protect prend un paramètre de texte en clair Byte [] et produit une charge utile protégée Byte [], dont le format est décrit ci-dessous. (Il existe également une surcharge de méthode d’extension qui prend un paramètre de chaîne en texte clair et retourne une charge utile protégée par une chaîne. Si cette API est utilisée, le format de la charge utile protégée aura toujours la structure ci-dessous, mais il sera [encodé base64url](https://tools.ietf.org/html/rfc4648#section-5).)

## <a name="protected-payload-format"></a>Format de charge utile protégée

Le format de charge utile protégée est constitué de trois composants principaux :

* En-tête magique 32 bits qui identifie la version du système de protection des données.

* ID de clé 128 bits qui identifie la clé utilisée pour protéger cette charge utile particulière.

* Le reste de la charge utile protégée est [spécifique au chiffreur encapsulé par cette clé](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Dans l’exemple ci-dessous, la clé représente un chiffreur AES-256-CBC + HMACSHA256, et la charge utile est sous-divisée comme suit :
  * Modificateur de clé 128 bits.
  * Vecteur d’initialisation 128 bits.
  * 48 octets de sortie AES-256-CBC.
  * Une balise d’authentification HMACSHA256.

Un exemple de charge utile protégée est illustré ci-dessous.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

À partir du format de charge utile au-dessus du premier 32 bits, ou 4 octets sont l’en-tête magique identifiant la version (09 F0 C9 F0)

Les 128 bits suivants, ou 16 octets sont l’identificateur de clé (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)

Le reste contient la charge utile et est spécifique au format utilisé.

> [!WARNING]
> Toutes les charges utiles protégées à une clé donnée commencent par le même en-tête de 20 octets (valeur magique, ID de clé). Les administrateurs peuvent utiliser ce fait à des fins de diagnostic approximatifs lorsqu’une charge utile a été générée. Par exemple, la charge utile ci-dessus correspond à la clé {0c819c80-6619-4019-9536-53f8aaffee57}. Si, après avoir vérifié le référentiel de clés, vous estimez que la date d’activation de cette clé spécifique était 2015-01-01 et que sa date d’expiration était 2015-03-01, il est raisonnable de supposer que la charge utile (si elle n’a pas été falsifiée) a été générée dans cette fenêtre, attribuez ou prenez un petit facteur manœuvre de chaque côté.
