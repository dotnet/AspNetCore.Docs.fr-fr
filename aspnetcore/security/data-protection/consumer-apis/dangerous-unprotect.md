---
title: Annuler la protection des charges utiles dont les clés ont été révoquées dans ASP.NET Core
author: rick-anderson
description: Découvrez comment ôter la protection des données, protégées par des clés qui ont été révoquées, dans une application ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5f13b53182f720ac8b58d90701561d381981308a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051094"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Annuler la protection des charges utiles dont les clés ont été révoquées dans ASP.NET Core

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

Les API de protection des données ASP.NET Core ne sont pas principalement destinées à la persistance illimitée des charges utiles confidentielles. D’autres technologies telles que [DPAPI Windows CNG](/windows/win32/seccng/cng-dpapi) et [Azure Rights Management](/rights-management/) sont plus adaptées au scénario de stockage indéfini, et elles ont des fonctionnalités de gestion de clés fortes. Cela dit, rien n’empêche un développeur d’utiliser les API de protection des données ASP.NET Core pour la protection à long terme des données confidentielles. Les clés ne sont jamais supprimées de l’anneau de clé `IDataProtector.Unprotect` . elles peuvent donc toujours récupérer les charges utiles existantes tant que les clés sont disponibles et valides.

Toutefois, un problème survient lorsque le développeur tente de déprotéger des données qui ont été protégées par une clé révoquée, comme `IDataProtector.Unprotect` lèvera une exception dans ce cas. Cela peut être utile pour les charges utiles courtes ou transitoires (comme les jetons d’authentification), car ces types de charges peuvent être facilement recréés par le système, et au pire, le visiteur du site peut être amené à se reconnecter. Toutefois, pour les charges utiles persistantes, l’utilisation de `Unprotect` throw peut entraîner une perte de données inacceptable.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Pour prendre en charge le scénario permettant d’empêcher la protection des charges utiles, même s’il s’agit de clés révoquées, le système de protection des données contient un `IPersistedDataProtector` type. Pour récupérer une instance de `IPersistedDataProtector` , récupérez simplement une instance de de `IDataProtector` manière normale et essayez d’effectuer un cast de `IDataProtector` en `IPersistedDataProtector` .

> [!NOTE]
> Toutes les `IDataProtector` instances ne peuvent pas être converties en `IPersistedDataProtector` . Les développeurs doivent utiliser l’opérateur as C# ou similaire pour éviter les exceptions d’exécution provoquées par des casts non valides, et ils doivent être prêts à gérer le cas d’échec de manière appropriée.

`IPersistedDataProtector` expose la surface d’API suivante :

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Cette API prend la charge utile protégée (comme un tableau d’octets) et retourne la charge utile non protégée. Il n’existe pas de surcharge basée sur une chaîne. Les deux paramètres out sont les suivants.

* `requiresMigration`: aura la valeur true si la clé utilisée pour protéger cette charge utile n’est plus la clé par défaut active, par exemple, la clé utilisée pour protéger cette charge utile est ancienne et une opération de déploiement de clé a eu lieu depuis. L’appelant peut souhaiter envisager de reprotéger la charge utile en fonction des besoins de l’entreprise.

* `wasRevoked`: aura la valeur true si la clé utilisée pour protéger cette charge utile a été révoquée.

>[!WARNING]
> Soyez extrêmement vigilant lorsque `ignoreRevocationErrors: true` vous passez à la `DangerousUnprotect` méthode. Si, après avoir appelé cette méthode `wasRevoked` , la valeur est true, la clé utilisée pour protéger cette charge utile a été révoquée et l’authenticité de la charge utile doit être considérée comme suspecte. Dans ce cas, continue à fonctionner sur la charge utile non protégée si vous avez un certain niveau d’authenticité, par exemple s’il provient d’une base de données sécurisée, plutôt que d’être envoyée par un client Web non approuvé.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
