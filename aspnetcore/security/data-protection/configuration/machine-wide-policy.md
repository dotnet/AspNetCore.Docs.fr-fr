---
title: Prise en charge des stratégies à l’ensemble de la protection des données dans ASP.NET Core
author: rick-anderson
description: En savoir plus sur la prise en charge de la définition d’une stratégie par défaut au niveau de l’ordinateur pour toutes les applications qui consomment ASP.NET Core protection des données.
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
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: f6f258e193bf964cb9b4cbd24da2740c5ed89a91
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052992"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Prise en charge des stratégies à l’ensemble de la protection des données dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

En cas d’exécution sous Windows, le système de protection des données offre une prise en charge limitée de la définition d’une stratégie par défaut à l’ensemble de l’ordinateur pour toutes les applications qui consomment ASP.NET Core protection des données. L’idée générale est qu’un administrateur peut souhaiter modifier un paramètre par défaut, tel que les algorithmes utilisés ou la durée de vie de la clé, sans qu’il soit nécessaire de mettre à jour manuellement chaque application sur l’ordinateur.

> [!WARNING]
> L’administrateur système peut définir la stratégie par défaut, mais ne peut pas l’appliquer. Le développeur de l’application peut toujours remplacer n’importe quelle valeur par l’un de leurs propres choix. La stratégie par défaut affecte uniquement les applications où le développeur n’a pas spécifié de valeur explicite pour un paramètre.

## <a name="setting-default-policy"></a>Définition de la stratégie par défaut

Pour définir la stratégie par défaut, un administrateur peut définir des valeurs connues dans le registre système sous la clé de Registre suivante :

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Si vous êtes sur un système d’exploitation 64 bits et que vous souhaitez affecter le comportement des applications 32 bits, n’oubliez pas de configurer l’équivalent Wow6432Node de la clé ci-dessus.

Les valeurs prises en charge sont indiquées ci-dessous.

| Valeur              | Type   | Description |
| ------------------ | :----: | ----------- |
| EncryptionType     | string | Spécifie les algorithmes à utiliser pour la protection des données. La valeur doit être CNG-CBC, CNG-GCM ou géré et est décrit plus en détail ci-dessous. |
| DefaultKeyLifetime | DWORD  | Spécifie la durée de vie des clés nouvellement générées. La valeur est spécifiée en jours et doit être >= 7. |
| KeyEscrowSinks     | string | Spécifie les types utilisés pour le dépôt de la clé. La valeur est une liste délimitée par des points-virgules de récepteurs de dépôt de clé, où chaque élément de la liste est le nom qualifié d’assembly d’un type qui implémente [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Types de chiffrement

Si EncryptionType est CNG-CBC, le système est configuré pour utiliser un chiffrement par bloc symétrique en mode CBC pour la confidentialité et HMAC pour l’authenticité avec les services fournis par Windows CNG (pour plus d’informations, consultez [spécification d’algorithmes personnalisés Windows CNG](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) ). Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété du type CngCbcAuthenticatedEncryptionSettings.

| Valeur                       | Type   | Description |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | Nom d’un algorithme de chiffrement par bloc symétrique compris par CNG. Cet algorithme est ouvert en mode CBC. |
| EncryptionAlgorithmProvider | string | Nom de l’implémentation du fournisseur CNG qui peut produire l’algorithme EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | Longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement par bloc symétrique. |
| HashAlgorithm               | string | Nom d’un algorithme de hachage compris par CNG. Cet algorithme est ouvert en mode HMAC. |
| HashAlgorithmProvider       | string | Nom de l’implémentation du fournisseur CNG qui peut produire l’algorithme HashAlgorithm. |

Si EncryptionType est CNG-GCM, le système est configuré pour utiliser un chiffrement par bloc symétrique en mode Galois/Counter pour la confidentialité et l’authenticité des services fournis par Windows CNG (pour plus d’informations, consultez [spécification d’algorithmes personnalisés Windows CNG](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) ). Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété du type CngGcmAuthenticatedEncryptionSettings.

| Valeur                       | Type   | Description |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | string | Nom d’un algorithme de chiffrement par bloc symétrique compris par CNG. Cet algorithme est ouvert en mode Galois/Counter. |
| EncryptionAlgorithmProvider | string | Nom de l’implémentation du fournisseur CNG qui peut produire l’algorithme EncryptionAlgorithm. |
| EncryptionAlgorithmKeySize  | DWORD  | Longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement par bloc symétrique. |

Si EncryptionType est géré, le système est configuré pour utiliser une SymmetricAlgorithm managée pour la confidentialité et KeyedHashAlgorithm pour l’authenticité (pour plus d’informations, consultez [spécification d’algorithmes managés personnalisés](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) ). Les valeurs supplémentaires suivantes sont prises en charge, chacune d’elles correspondant à une propriété du type ManagedAuthenticatedEncryptionSettings.

| Valeur                      | Type   | Description |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | string | Nom qualifié d’assembly d’un type qui implémente SymmetricAlgorithm. |
| EncryptionAlgorithmKeySize | DWORD  | Longueur (en bits) de la clé à dériver pour l’algorithme de chiffrement symétrique. |
| ValidationAlgorithmType    | string | Nom qualifié d’assembly d’un type qui implémente KeyedHashAlgorithm. |

Si EncryptionType a une autre valeur que null ou vide, le système de protection des données lève une exception au démarrage.

> [!WARNING]
> Lors de la configuration d’un paramètre de stratégie par défaut qui implique des noms de types (EncryptionAlgorithmType, ValidationAlgorithmType, KeyEscrowSinks), les types doivent être disponibles pour l’application. Cela signifie que pour les applications qui s’exécutent sur Desktop CLR, les assemblys qui contiennent ces types doivent être présents dans le global assembly cache (GAC). Pour les applications ASP.NET Core s’exécutant sur .NET Core, les packages qui contiennent ces types doivent être installés.
