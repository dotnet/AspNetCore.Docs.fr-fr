---
title: Scénarios non compatibles avec l’injection de dépendances pour la protection des données dans ASP.NET Core
author: rick-anderson
description: Découvrez comment prendre en charge les scénarios de protection des données dans lesquels vous ne pouvez pas ou ne souhaitez pas utiliser un service fourni par l’injection de dépendances.
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
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 03257596cafd9ec99f90b44d8fcb878b6747ba39
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052823"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Scénarios non compatibles avec l’injection de dépendances pour la protection des données dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Le système de protection des données ASP.NET Core est normalement [ajouté à un conteneur de service](xref:security/data-protection/consumer-apis/overview) et consommé par des composants dépendants via l’injection de dépendances (di). Toutefois, il existe des cas où cela n’est pas faisable ou souhaité, en particulier lors de l’importation du système dans une application existante.

Pour prendre en charge ces scénarios, le package [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) fournit un type concret, [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), qui offre un moyen simple d’utiliser la protection des données sans compter sur di. Le `DataProtectionProvider` type implémente [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). La construction `DataProtectionProvider` nécessite uniquement la fourniture d’une instance [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pour indiquer l’emplacement de stockage des clés de chiffrement du fournisseur, comme illustré dans l’exemple de code suivant :

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

Par défaut, le `DataProtectionProvider` type concret ne chiffre pas le matériel de clé brut avant de le rendre persistant dans le système de fichiers. Cela permet de prendre en charge les scénarios dans lesquels le développeur pointe vers un partage réseau et le système de protection des données ne peut pas déduire automatiquement un mécanisme de chiffrement à clé inactive approprié.

En outre, le `DataProtectionProvider` type concret n' [isole](xref:security/data-protection/configuration/overview#per-application-isolation) pas les applications par défaut. Toutes les applications qui utilisent le même répertoire de clé peuvent partager des charges utiles tant que leurs [paramètres d’objectif](xref:security/data-protection/consumer-apis/purpose-strings) correspondent.

Le constructeur [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) accepte un rappel de configuration facultatif qui peut être utilisé pour ajuster les comportements du système. L’exemple ci-dessous illustre la restauration de l’isolation avec un appel explicite à [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname). L’exemple illustre également la configuration du système pour chiffrer automatiquement les clés persistantes à l’aide de Windows DPAPI. Si le répertoire pointe vers un partage UNC, vous souhaiterez peut-être distribuer un certificat partagé sur tous les ordinateurs concernés et configurer le système pour qu’il utilise le chiffrement basé sur les certificats avec un appel à [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate).

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> Les instances du `DataProtectionProvider` type concret sont coûteuses à créer. Si une application gère plusieurs instances de ce type et si elles utilisent toutes le même répertoire de stockage de clés, les performances de l’application peuvent se dégrader. Si vous utilisez le `DataProtectionProvider` type, nous vous recommandons de créer ce type une seule fois et de le réutiliser autant que possible. Le `DataProtectionProvider` type et toutes les instances [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) créées à partir de celui-ci sont thread-safe pour plusieurs appelants.
