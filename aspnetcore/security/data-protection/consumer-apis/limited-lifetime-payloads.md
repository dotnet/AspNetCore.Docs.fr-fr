---
title: Limiter la durée de vie des charges utiles protégées dans ASP.NET Core
author: rick-anderson
description: Découvrez comment limiter la durée de vie d’une charge utile protégée à l’aide des API de protection des données ASP.NET Core.
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
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 74417d076399066a49271c27ff128d9de6c10f94
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060792"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Limiter la durée de vie des charges utiles protégées dans ASP.NET Core

Il existe des scénarios dans lesquels le développeur d’applications souhaite créer une charge utile protégée qui expire après un laps de temps défini. Par exemple, la charge utile protégée peut représenter un jeton de réinitialisation de mot de passe qui ne doit être valide que pendant une heure. Il est certes possible pour le développeur de créer son propre format de charge utile qui contient une date d’expiration incorporée, et les développeurs avancés peuvent souhaiter le faire tout de même, mais pour la plupart des développeurs qui gèrent ces expirations peuvent devenir fastidieux.

Pour faciliter cette tâche pour les développeurs, le package [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contient des API d’utilitaire pour créer des charges utiles qui expirent automatiquement après un laps de temps défini. Ces API se bloquent du `ITimeLimitedDataProtector` type.

## <a name="api-usage"></a>Utilisation des API

L' `ITimeLimitedDataProtector` interface est l’interface principale pour la protection et la déprotection des charges utiles limitées dans le temps ou à l’expiration automatique. Pour créer une instance d’un `ITimeLimitedDataProtector` objet, vous avez d’abord besoin d’une instance d’un [IDataProtector](xref:security/data-protection/consumer-apis/overview) standard construit avec un objectif spécifique. Une fois l' `IDataProtector` instance disponible, appelez la `IDataProtector.ToTimeLimitedDataProtector` méthode d’extension pour obtenir un protecteur avec des fonctionnalités d’expiration intégrées.

`ITimeLimitedDataProtector` expose les méthodes d’extension et la surface d’API suivantes :

* CreateProtector (String Purpose) : ITimeLimitedDataProtector : cette API est similaire à la existante `IDataProtectionProvider.CreateProtector` dans la mesure où elle peut être utilisée pour créer des [chaînes d’objectif](xref:security/data-protection/consumer-apis/purpose-strings) à partir d’un protecteur à durée limitée.

* Protect (Byte [] texte en clair, expiration DateTimeOffset) : Byte []

* Protect (Byte [] texte en clair, durée de vie TimeSpan) : Byte []

* Protect (Byte [] texte en clair) : Byte []

* Protect (chaîne en texte clair, expiration DateTimeOffset) : chaîne

* Protect (chaîne en clair, durée de vie de la période) : chaîne

* Protect (chaîne de texte en clair) : chaîne

En plus des méthodes principales `Protect` qui prennent uniquement le texte en clair, il existe de nouvelles surcharges qui permettent de spécifier la date d’expiration de la charge utile. La date d’expiration peut être spécifiée sous la forme d’une date absolue (par `DateTimeOffset` exemple,) ou sous la forme d’un temps relatif (à partir de l’heure système actuelle, via un `TimeSpan` ). Si une surcharge qui ne prend pas d’expiration est appelée, la charge utile est censée ne jamais expirer.

* UnProtection (Byte [] protectedData, out DateTimeOffset expiration) : Byte []

* Unprotect (Byte [] protectedData) : Byte []

* UnProtection (String protectedData, out DateTimeOffset expiration) : chaîne

* Unprotect (chaîne protectedData) : chaîne

Les `Unprotect` méthodes retournent les données non protégées d’origine. Si la charge utile n’a pas encore expiré, l’expiration absolue est retournée en tant que paramètre de sortie facultatif avec les données non protégées d’origine. Si la charge utile est expirée, toutes les surcharges de la méthode Unprotect lèvent CryptographicException.

>[!WARNING]
> Il n’est pas recommandé d’utiliser ces API pour protéger les charges utiles qui nécessitent une persistance à long terme ou indéfinie. « Puis-je me permettre que les charges utiles protégées soient irrémédiablement irrémédiables après un mois ? » peut servir de bonne règle de pouce ; Si la réponse n’est pas, les développeurs doivent envisager d’autres API.

L’exemple ci-dessous utilise les [chemins d’accès de code non-di](xref:security/data-protection/configuration/non-di-scenarios) pour instancier le système de protection des données. Pour exécuter cet exemple, assurez-vous d’avoir d’abord ajouté une référence au package Microsoft. AspNetCore. DataProtection. extensions.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
