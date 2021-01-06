---
title: Configurer le massicot pour ASP.NET Core Blazor
author: guardrex
description: Découvrez comment contrôler l’éditeur de liens en langage intermédiaire (massicot) lors de la création d’une Blazor application.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055800"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>Configurer le massicot pour ASP.NET Core Blazor

Par [Pranav Krishnamoorthy](https://github.com/pranavkm)

Blazor WebAssembly effectue un découpage en [langage intermédiaire (il)](/dotnet/standard/managed-code#intermediate-language--execution) pour réduire la taille de la sortie publiée.

Le découpage d’une application optimise sa taille, mais peut avoir des effets néfastes. Les applications qui utilisent la réflexion ou les fonctionnalités dynamiques associées peuvent s’arrêter quand elles sont tronquées, car le massicot n’a pas connaissance du comportement dynamique et ne peut pas déterminer en général les types requis pour la réflexion au moment de l’exécution. Pour supprimer de telles applications, le massicot doit être informé des types requis par la réflexion dans le code et dans les packages ou infrastructures dont dépend l’application.

Pour garantir le bon fonctionnement de l’application tronquée une fois déployée, il est important de tester fréquemment la sortie publiée lors du développement.

Le filtrage pour les applications .NET peut être désactivé en affectant `PublishTrimmed` à la propriété MSBuild la valeur `false` dans le fichier projet de l’application :

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
Vous trouverez des options supplémentaires pour configurer le massicot dans [options de suppression](/dotnet/core/deploying/trimming-options).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Supprimer les exécutables et les déploiements autonomes](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
