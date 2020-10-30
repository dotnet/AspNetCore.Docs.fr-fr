---
title: Dérivation de sous-clé et chiffrement authentifié dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation de la dérivation des sous-clés de protection des données ASP.NET Core et du chiffrement authentifié.
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
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: efe8ad2f71feda9cbc1693d362e30eff29cbcd74
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060155"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>Dérivation de sous-clé et chiffrement authentifié dans ASP.NET Core

<a name="data-protection-implementation-subkey-derivation"></a>

La plupart des clés dans l’anneau de clé contiendront une certaine forme d’entropie et auront des informations algorithmiques indiquant « CBC-mode Encryption + HMAC validation » ou « GCM Encryption + validation ». Dans ce cas, nous faisons référence à l’entropie incorporée en tant que support de génération de clé principal (ou KM) pour cette clé, et nous effectuons une fonction de dérivation de clé pour dériver les clés qui seront utilisées pour les opérations de chiffrement réelles.

> [!NOTE]
> Les clés sont abstraites et une implémentation personnalisée peut ne pas se comporter comme indiqué ci-dessous. Si la clé fournit sa propre implémentation de `IAuthenticatedEncryptor` plutôt que d’utiliser l’une de nos fabriques intégrées, le mécanisme décrit dans cette section ne s’applique plus.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Données authentifiées supplémentaires et dérivation de sous-clé

L' `IAuthenticatedEncryptor` interface sert d’interface principale pour toutes les opérations de chiffrement authentifiées. Sa `Encrypt` méthode prend deux mémoires tampons : texte en clair et additionalAuthenticatedData (AAD). Le contenu en texte en clair n’a pas changé l’appel à `IDataProtector.Protect` , mais AAD est généré par le système et se compose de trois composants :

1. En-tête magique 32 bits 09 F0 C9 F0 qui identifie cette version du système de protection des données.

2. ID de la clé 128 bits.

3. Chaîne de longueur variable formée à partir de la chaîne d’objectif qui a créé le `IDataProtector` qui effectue cette opération.

Étant donné que AAD est unique pour le tuple des trois composants, nous pouvons l’utiliser pour dériver de nouvelles clés de KM au lieu d’utiliser la valeur KM proprement dite dans toutes nos opérations de chiffrement. Pour chaque appel à `IAuthenticatedEncryptor.Encrypt` , le processus de dérivation de clé suivant a lieu :

`( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)`

Ici, nous appelons le NIST SP800-108 KDF en mode compteur (voir [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5,1) avec les paramètres suivants :

* Clé de dérivation de clé (KDK) = `K_M`

* PRF = HMACSHA512

* étiquette = additionalAuthenticatedData

* Context = contextHeader | | keymodifier

L’en-tête de contexte est de longueur variable et sert essentiellement d’empreinte numérique des algorithmes pour lesquels nous allons dériver `K_E` et `K_H` . Le modificateur de clé est une chaîne 128 bits générée de façon aléatoire pour chaque appel à `Encrypt` et sert à garantir une probabilité écrasante que ke et KH sont uniques pour cette opération de chiffrement d’authentification spécifique, même si toutes les autres entrées du KDF sont constantes.

Pour les opérations de chiffrement en mode CBC + validation HMAC, `| K_E |` est la longueur de la clé de chiffrement par bloc symétrique et `| K_H |` est la taille Digest de la routine HMAC. Pour les opérations de chiffrement GCM + validation, `| K_H | = 0` .

## <a name="cbc-mode-encryption--hmac-validation"></a>Chiffrement en mode CBC + validation HMAC

Une fois que `K_E` est généré via le mécanisme ci-dessus, nous générons un vecteur d’initialisation aléatoire et exécutons l’algorithme de chiffrement par bloc symétrique pour chiffrer le texte en clair. Le vecteur d’initialisation et le texte chiffré sont ensuite exécutés via la routine HMAC initialisée avec la clé `K_H` pour produire le Mac. Ce processus et la valeur de retour sont représentés graphiquement ci-dessous.

![Processus et retour CBC en mode](subkeyderivation/_static/cbcprocess.png)

`output:= keyModifier || iv || E_cbc (K_E,iv,data) || HMAC(K_H, iv || E_cbc (K_E,iv,data))`

> [!NOTE]
> L' `IDataProtector.Protect` implémentation [ajoute l’en-tête et l’ID de clé Magic](xref:security/data-protection/implementation/authenticated-encryption-details) à la sortie avant de la renvoyer à l’appelant. Étant donné que l’en-tête Magic et l’ID de clé font implicitement partie d' [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), et que le modificateur de clé est alimenté comme entrée à KDF, cela signifie que chaque octet de la charge utile finale retournée est authentifié par le Mac.

## <a name="galoiscounter-mode-encryption--validation"></a>Chiffrement et validation du mode Galois/Counter

Une fois `K_E` générée via le mécanisme ci-dessus, nous générons une valeur à usage unique 96 bits aléatoire et exécutons l’algorithme de chiffrement par bloc symétrique pour chiffrer le texte en clair et produire la balise d’authentification 128 bits.

![Processus GCM-mode et retour](subkeyderivation/_static/galoisprocess.png)

`output := keyModifier || nonce || E_gcm (K_E,nonce,data) || authTag`

> [!NOTE]
> Bien que GCM prenne en charge nativement le concept d’AAD, nous n’utilisons toujours AAD que dans le KDF d’origine, en choisissant de passer une chaîne vide dans GCM pour son paramètre AAD. La raison en est le double. Tout d’abord, [pour prendre en charge l’agilité](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) , nous ne souhaitons jamais utiliser `K_M` directement comme clé de chiffrement. En outre, GCM impose des exigences d’unicité très strictes à ses entrées. La probabilité que la routine de chiffrement GCM soit appelée sur deux ou plusieurs jeux de données d’entrée distincts avec la même paire (clé, nonce) ne doit pas dépasser 2 ^ 32. Si nous corrigeons que nous `K_E` ne pouvons pas effectuer plus de 2 ^ 32 opérations de chiffrement avant d’exécuter le afoul de la limite de 2 ^-32. Cela peut sembler un très grand nombre d’opérations, mais un serveur Web à trafic élevé peut passer par 4 milliards demandes en quelques jours, et dans la durée de vie normale de ces clés. Pour rester conforme de la limite de probabilité de 2 ^ 32, nous continuons d’utiliser un modificateur de clé 128 bits et une valeur à usage unique de 96 bits, qui étend radicalement le nombre d’opérations utilisables pour un donné `K_M` . Pour simplifier la conception, nous partageons le chemin de code KDF entre les opérations CBC et GCM, et dans la mesure où AAD est déjà pris en compte dans le KDF, il n’est pas nécessaire de le transférer à la routine GCM.
