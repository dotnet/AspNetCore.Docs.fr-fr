---
title: Immuabilité des clés et paramètres de clé dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation des API d’immuabilité de la clé de protection des données ASP.NET Core.
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061247"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Immuabilité des clés et paramètres de clé dans ASP.NET Core

Une fois qu’un objet est rendu persistant dans le magasin de stockage, sa représentation est toujours fixe. De nouvelles données peuvent être ajoutées au magasin de stockage, mais les données existantes ne peuvent jamais être mutées. L’objectif principal de ce comportement est d’empêcher la corruption des données.

L’une des conséquences de ce comportement est qu’une fois qu’une clé est écrite dans le magasin de stockage, elle est immuable. Ses dates de création, d’activation et d’expiration ne peuvent jamais être modifiées, même si elles peuvent être révoquées à l’aide de `IKeyManager` . En outre, ses informations algorithmiques sous-jacentes, le matériel de clé principale et le chiffrement au repos sont également immuables.

Si le développeur modifie un paramètre qui affecte la persistance des clés, ces modifications n’entrent pas en vigueur jusqu’à la prochaine génération d’une clé, par le biais d’un appel explicite à `IKeyManager.CreateNewKey` ou via le comportement de [génération automatique de la clé automatique](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) du système de protection des données. Les paramètres qui affectent la persistance des clés sont les suivants :

* [Durée de vie de la clé par défaut](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [Mécanisme de chiffrement à clé au repos](xref:security/data-protection/implementation/key-encryption-at-rest)

* [Informations algorithmiques contenues dans la clé](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Si vous avez besoin que ces paramètres démarrent avant la prochaine période de répercussion de clé automatique, envisagez d’effectuer un appel explicite à `IKeyManager.CreateNewKey` pour forcer la création d’une nouvelle clé. N’oubliez pas de fournir une date d’activation explicite ({Now + 2 days} est une bonne règle empirique pour permettre le temps nécessaire à la propagation de la modification) et la date d’expiration dans l’appel.

>[!TIP]
> Toutes les applications qui touchent le référentiel doivent spécifier les mêmes paramètres avec les `IDataProtectionBuilder` méthodes d’extension. Dans le cas contraire, les propriétés de la clé persistante dépendent de l’application qui a appelé les routines de génération de clé.
