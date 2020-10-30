---
title: Gestion et durée de vie des clés de protection des données dans ASP.NET Core
author: rick-anderson
description: En savoir plus sur la gestion et la durée de vie des clés de protection des données dans ASP.NET Core.
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
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 1303c5c2c993f1d20383457666aebfa2a583e938
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053005"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>Gestion et durée de vie des clés de protection des données dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Gestion des clés

L’application tente de détecter son environnement d’exploitation et de gérer la configuration de clé proprement dit.

1. Si l’application est hébergée dans des [applications Azure](https://azure.microsoft.com/services/app-service/), les clés sont conservées dans le dossier *%Home%\ASP.NET\DataProtection-Keys* . Ce dossier est alimenté par le stockage réseau et synchronisé sur tous les ordinateurs hébergeant l’application.
   * Les clés ne sont pas protégées au repos.
   * Le dossier *dataprotection-Keys* fournit l’anneau clé à toutes les instances d’une application dans un seul emplacement de déploiement.
   * Les emplacements de déploiement distincts, tels que Préproduction et Production, ne partagent pas de porte-clés. Lorsque vous basculez entre les emplacements de déploiement, par exemple en échange de la mise en lots vers la production ou à l’aide d’un test A/B, toute application utilisant la protection des données ne pourra pas déchiffrer les données stockées à l’aide de l’anneau de clé à l’intérieur de l’emplacement précédent. Cela amène les utilisateurs à se déconnecter d’une application qui utilise l’authentification ASP.NET Core standard cookie , car elle utilise la protection des données pour protéger ses cookie s. Si vous souhaitez des sonneries de clé indépendantes du logement, utilisez un fournisseur de sonneries de clé externe, tel que le stockage d’objets BLOB Azure, Azure Key Vault, un magasin SQL ou un cache ReDim.

1. Si le profil utilisateur est disponible, les clés sont conservées dans le dossier *%LocalAppData%\ASP.NET\DataProtection-Keys* . Si le système d’exploitation est Windows, les clés sont chiffrées au repos à l’aide de DPAPI.

   L’[attribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) du pool d’applications doit également être activé. La valeur par défaut de `setProfileEnvironment` est `true`. Dans certains scénarios (par exemple pour le système d’exploitation Windows), `setProfileEnvironment` est défini sur `false`. Si les clés ne sont pas stockées dans le répertoire de profil utilisateur comme prévu :

   1. Accédez au dossier *%windir%/system32/inetsrv/config* .
   1. Ouvrez le fichier *applicationHost.config* .
   1. Recherchez l’élément `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
   1. Confirmez que l’attribut `setProfileEnvironment` n’est pas présent, ce qui implique que la valeur par défaut est `true`, ou définissez de manière explicite la valeur de l’attribut sur `true`.

1. Si l’application est hébergée dans IIS, les clés sont conservées dans le Registre HKLM dans une clé de Registre spéciale qui est gérée uniquement au compte du processus de travail. Les clés sont chiffrées au repos à l’aide de DPAPI.

1. Si aucune de ces conditions ne correspond, les clés ne sont pas conservées en dehors du processus en cours. Lorsque le processus s’arrête, toutes les clés générées sont perdues.

Le développeur est toujours en contrôle total et peut remplacer le mode et l’emplacement de stockage des clés. Les trois premières options ci-dessus doivent fournir des valeurs par défaut appropriées pour la plupart des applications similaires à la façon dont les **\<machineKey>** routines de génération automatique ASP.net fonctionnaient dans le passé. L’option de secours finale est le seul scénario qui requiert que le développeur spécifie la [configuration](xref:security/data-protection/configuration/overview) avant s’il souhaite une persistance des clés, mais ce secours ne se produit que dans de rares cas.

Lors de l’hébergement dans un conteneur d’ancrage, les clés doivent être rendues persistantes dans un dossier qui est un volume d’ancrage (un volume partagé ou un volume monté sur l’hôte qui persiste au-delà de la durée de vie du conteneur) ou dans un fournisseur externe, tel que [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ou des [ReDim](https://redis.io/). Un fournisseur externe est également utile dans les scénarios de batterie de serveurs Web si les applications ne peuvent pas accéder à un volume réseau partagé (pour plus d’informations, consultez [PersistKeysToFileSystem](xref:security/data-protection/configuration/overview#persistkeystofilesystem) ).

> [!WARNING]
> Si le développeur remplace les règles décrites ci-dessus et pointe le système de protection des données dans un référentiel de clé spécifique, le chiffrement automatique des clés au repos est désactivé. La protection au repos peut être réactivée via la [configuration](xref:security/data-protection/configuration/overview).

## <a name="key-lifetime"></a>Durée de vie de la clé

Par défaut, les clés ont une durée de vie de 90 jours. Lorsqu’une clé expire, l’application génère automatiquement une nouvelle clé et définit la nouvelle clé comme clé active. Tant que les clés retirées restent sur le système, votre application peut déchiffrer toutes les données protégées avec elles. Pour plus d’informations, consultez [gestion des clés](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) .

## <a name="default-algorithms"></a>Algorithmes par défaut

L’algorithme de protection par charge utile par défaut utilisé est AES-256-CBC pour la confidentialité et HMACSHA256 pour l’authenticité. Une clé principale 512 bits, modifiée tous les 90 jours, est utilisée pour dériver les deux sous-clés utilisées pour ces algorithmes pour chaque charge utile. Pour plus d’informations, consultez [dérivation de sous-clés](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) .

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
