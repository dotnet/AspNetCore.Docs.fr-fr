---
title: Hacher les mots de passe dans ASP.NET Core
author: rick-anderson
description: Découvrez comment hacher les mots de passe à l’aide des API de protection des données ASP.NET Core.
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: b88abd076febae95f8990bcd730ce0d4517e92d2
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587760"
---
# <a name="hash-passwords-in-aspnet-core"></a>Hacher les mots de passe dans ASP.NET Core

La base de code de protection des données inclut un package *Microsoft. AspNetCore. Cryptography. keydérivation* qui contient des fonctions de dérivation de clé de chiffrement. Ce package est un composant autonome et n’a aucune dépendance vis-à-vis du reste du système de protection des données. Il peut être utilisé entièrement indépendamment. La source existe avec la base de code de protection des données pour des raisons pratiques.

Le package offre actuellement une méthode `KeyDerivation.Pbkdf2` qui autorise le hachage d’un mot de passe à l’aide de l' [algorithme PBKDF2](https://tools.ietf.org/html/rfc2898#section-5.2). Cette API est très similaire au [type Rfc2898DeriveBytes](/dotnet/api/system.security.cryptography.rfc2898derivebytes)existant de .NET Framework, mais il existe trois différences importantes :

1. La `KeyDerivation.Pbkdf2` méthode prend en charge l’utilisation de plusieurs PRFs (actuellement `HMACSHA1` , `HMACSHA256` et `HMACSHA512` ), tandis que le `Rfc2898DeriveBytes` type prend en charge uniquement `HMACSHA1` .

2. La `KeyDerivation.Pbkdf2` méthode détecte le système d’exploitation actuel et tente de choisir l’implémentation la plus optimisée de la routine, offrant ainsi de meilleures performances dans certains cas. (Sur Windows 8, il offre environ 10 fois plus de débit `Rfc2898DeriveBytes` .)

3. La `KeyDerivation.Pbkdf2` méthode impose à l’appelant de spécifier tous les paramètres (Salt, PRF et nombre d’itérations). Le `Rfc2898DeriveBytes` type fournit des valeurs par défaut pour ces.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Consultez le [code source](https://github.com/dotnet/AspNetCore/blob/main/src/Identity/Extensions.Core/src/PasswordHasher.cs) du ASP.NET Core Identity `PasswordHasher` type de pour un cas d’usage réel.
