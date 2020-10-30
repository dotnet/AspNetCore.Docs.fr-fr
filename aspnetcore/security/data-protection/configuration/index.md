---
title: Configuration de la protection des données dans ASP.NET Core
author: rick-anderson
description: Consultez les rubriques qui expliquent comment configurer la protection des données dans ASP.NET Core.
ms.author: riande
ms.date: 10/12/2017
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
uid: security/data-protection/configuration/index
ms.openlocfilehash: 4b2e9b376df8d1904f550be46b2e1849c4c08e54
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053018"
---
# <a name="data-protection-configuration-in-aspnet-core"></a>Configuration de la protection des données dans ASP.NET Core

Consultez les rubriques suivantes pour en savoir plus sur la configuration de la protection des données dans ASP.NET Core :

* [Configurer la protection des données ASP.NET Core](xref:security/data-protection/configuration/overview)  
  Vue d’ensemble de la configuration de la protection des données ASP.NET Core.

* [Gestion et durée de vie des clés de protection des données](xref:security/data-protection/configuration/default-settings)  
  Informations sur la gestion et la durée de vie des clés de protection des données.

* [Prise en charge de la stratégie au niveau de l’ordinateur de protection des données](xref:security/data-protection/configuration/machine-wide-policy)  
  Informations sur la définition d’une stratégie par défaut au niveau de l’ordinateur pour toutes les applications qui utilisent la protection des données.

* [Scénarios non compatibles avec l’injection de dépendances pour la protection des données dans ASP.NET Core](xref:security/data-protection/configuration/non-di-scenarios)  
  Indique comment utiliser le type concret [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider) pour utiliser la protection des données sans passer par des chemins de code spécifiques à l’injection de dépendances.
