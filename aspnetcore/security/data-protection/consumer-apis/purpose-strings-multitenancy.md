---
title: Hiérarchie des objectifs et architecture mutualisée dans ASP.NET Core
author: rick-anderson
description: En savoir plus sur la hiérarchie des chaînes à usage général et sur l’architecture mutualisée en ce qui concerne les API de protection des données ASP.NET Core.
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
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 84714cd852a3cbc7ff77d0fd0c88931536dead1a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052784"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Hiérarchie des objectifs et architecture mutualisée dans ASP.NET Core

Étant donné qu’un `IDataProtector` est également implicitement un `IDataProtectionProvider` , les objectifs peuvent être chaînés ensemble. Dans ce sens, `provider.CreateProtector([ "purpose1", "purpose2" ])` est équivalent à `provider.CreateProtector("purpose1").CreateProtector("purpose2")` .

Cela permet d’obtenir des relations hiérarchiques intéressantes via le système de protection des données. Dans l’exemple précédent de [contoso. Messaging. SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose), le composant SecureMessage peut appeler `provider.CreateProtector("Contoso.Messaging.SecureMessage")` une fois en amont et mettre en cache le résultat dans un `_myProvider` champ privé. Des protecteurs futurs peuvent ensuite être créés via des appels à `_myProvider.CreateProtector("User: username")` et ces protecteurs sont utilisés pour sécuriser les messages individuels.

Cela peut également être retourné. Prenons l’exemple d’une application logique unique qui héberge plusieurs locataires (un CMS paraît raisonnable) et chaque locataire peut être configuré avec son propre système de gestion d’État et d’authentification. L’application parapluie a un seul fournisseur principal, et elle appelle `provider.CreateProtector("Tenant 1")` et `provider.CreateProtector("Tenant 2")` donne à chaque locataire sa propre tranche isolée du système de protection des données. Les locataires peuvent ensuite dériver leurs propres Protecteurs individuels en fonction de leurs propres besoins, mais quelle que soit leur nature, ils ne peuvent pas créer de protecteurs qui sont en conflit avec les autres locataires du système. Graphiquement, ceci est représenté comme indiqué ci-dessous.

![À des fins d’architecture mutualisée](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Cela suppose que l’application parapluie contrôle les API qui sont disponibles pour les locataires individuels et que les locataires ne peuvent pas exécuter du code arbitraire sur le serveur. Si un locataire peut exécuter du code arbitraire, il peut effectuer une réflexion privée pour rompre les garanties d’isolation, ou il peut simplement lire directement le matériel de génération de clé principal et dériver les sous-clés qu’il souhaite.

Le système de protection des données utilise en fait une sorte d’architecture mutualisée dans sa configuration prête à l’emploi par défaut. Par défaut, le matériel de génération de clé principal est stocké dans le dossier de profil utilisateur du compte de processus de travail (ou dans le registre, pour les identités du pool d’applications IIS). Mais il est en fait relativement courant d’utiliser un seul compte pour exécuter plusieurs applications, et par conséquent, toutes ces applications finissent par partager le matériel de génération de clés principal. Pour résoudre ce cas, le système de protection des données insère automatiquement un identificateur unique par application comme premier élément de la chaîne à usage général. Cet objectif implicite sert à [isoler les applications individuelles](xref:security/data-protection/configuration/overview#per-application-isolation) les unes des autres en traitant efficacement chaque application comme un locataire unique au sein du système, et le processus de création du protecteur est identique à l’image ci-dessus.
