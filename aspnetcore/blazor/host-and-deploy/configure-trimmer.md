---
title: Configurer le massicot pour ASP.NET Core Blazor
author: guardrex
description: Découvrez comment contrôler l’éditeur de liens en langage intermédiaire (massicot) lors de la création d’une Blazor application.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975211"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a>Configurer le massicot pour ASP.NET Core Blazor

Blazor WebAssembly effectue un découpage en [langage intermédiaire (il)](/dotnet/standard/managed-code#intermediate-language--execution) pour réduire la taille de la sortie publiée. Par défaut, la suppression se produit lors de la publication d’une application.

Le découpage peut avoir des effets néfastes. Dans les applications qui utilisent la réflexion, le massicot ne peut souvent pas déterminer les types requis pour la réflexion au moment de l’exécution. Pour supprimer les applications qui utilisent la réflexion, le massicot doit être informé des types requis pour la réflexion dans le code de l’application et dans les packages ou infrastructures dont dépend l’application. Le massicot ne peut pas non plus réagir au comportement dynamique d’une application au moment de l’exécution. Pour garantir le bon fonctionnement de l’application tronquée une fois déployée, testez fréquemment la sortie publiée lors du développement.

Pour configurer le massicot, consultez l’article sur les [options de suppression](/dotnet/core/deploying/trimming-options) dans la documentation sur les notions de base de .net, qui comprend des conseils sur les sujets suivants :

* Désactivez le filtrage pour l’application entière avec la `<PublishTrimmed>` propriété dans le fichier projet.
* Contrôler la façon dont le langage intermédiaire inutilisé est supprimé par le massicot.
* Arrêtez le massicot pour découper des assemblys spécifiques.
* Assemblys « racine » à découper.
* Avertissements de surface pour les types réfléchis en affectant à la propriété la valeur `<SuppressTrimAnalysisWarnings>` `false` dans le fichier projet.
* Contrôle du découpage des symboles et de la prise en charge degugger.
* Définissez les fonctionnalités du massicot pour la suppression des fonctionnalités de la bibliothèque d’infrastructure.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Supprimer les exécutables et les déploiements autonomes](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
