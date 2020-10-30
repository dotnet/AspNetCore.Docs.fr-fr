---
title: Gestion des clés dans ASP.NET Core
author: rick-anderson
description: Découvrez les détails de l’implémentation des API de gestion de clés de protection des données ASP.NET Core.
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
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: f7e0fa5e80208469188a11239a9453ecafdcf5f3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060194"
---
# <a name="key-management-in-aspnet-core"></a>Gestion des clés dans ASP.NET Core

<a name="data-protection-implementation-key-management"></a>

Le système de protection des données gère automatiquement la durée de vie des clés principales utilisées pour protéger et ôter la protection des charges utiles. Chaque clé peut exister dans l’une des quatre étapes suivantes :

* Créé : la clé existe dans le Ring Key, mais n’a pas encore été activée. La clé ne doit pas être utilisée pour les nouvelles opérations de protection jusqu’à ce que la durée de la propagation de la clé soit suffisante pour tous les ordinateurs qui consomment cette sonnerie de clé.

* Actif : la clé existe dans l’anneau clé et doit être utilisée pour toutes les nouvelles opérations de protection.

* Expiré : la clé a exécuté sa durée de vie naturelle et ne doit plus être utilisée pour les nouvelles opérations de protection.

* Révoqué : la clé est compromise et ne doit pas être utilisée pour les nouvelles opérations de protection.

Les clés créées, actives et expirées peuvent toutes être utilisées pour ôter la protection des charges utiles entrantes. Les clés révoquées par défaut ne peuvent pas être utilisées pour ôter la protection des charges utiles, mais le développeur de l’application peut [remplacer ce comportement](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) si nécessaire.

>[!WARNING]
> Le développeur peut être tenté de supprimer une clé de l’anneau clé (par exemple, en supprimant le fichier correspondant du système de fichiers). À ce stade, toutes les données protégées par la clé sont définitivement déchiffrables, et il n’y a pas de remplacement d’urgence comme c’est le cas avec les clés révoquées. La suppression d’une clé est véritablement un comportement destructif et, par conséquent, le système de protection des données n’expose aucune API de première classe pour effectuer cette opération.

## <a name="default-key-selection"></a>Sélection de clé par défaut

Lorsque le système de protection des données lit l’anneau de clé à partir du référentiel de sauvegarde, il tente de localiser une clé « par défaut » dans l’anneau de clé. La clé par défaut est utilisée pour les nouvelles opérations de protection.

L’heuristique générale est que le système de protection des données choisit la clé avec la date d’activation la plus récente comme clé par défaut. (Il y a un petit facteur manœuvre pour permettre la distorsion des horloges de serveur à serveur.) Si la clé a expiré ou est révoquée, et si l’application n’a pas désactivé la génération de clé automatique, une nouvelle clé sera générée avec l’activation immédiate en fonction de la stratégie [d’expiration et de déroulement](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) de la clé ci-dessous.

La raison pour laquelle le système de protection des données génère une nouvelle clé immédiatement, plutôt que de revenir à une clé différente, est que la nouvelle génération de clé doit être traitée comme une expiration implicite de toutes les clés qui ont été activées avant la nouvelle clé. L’idée générale est que de nouvelles clés peuvent avoir été configurées avec des algorithmes ou des mécanismes de chiffrement au repos différents de ceux des anciennes clés, et que le système doit préférer la configuration actuelle à la restauration.

Il y a une exception. Si le développeur de l’application a [désactivé la génération de clé automatique](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), le système de protection des données doit choisir un nom comme clé par défaut. Dans ce scénario de secours, le système choisit la clé non révoquée avec la date d’activation la plus récente, avec la préférence donnée aux clés qui ont eu le temps de se propager aux autres ordinateurs du cluster. En conséquence, le système de secours peut finir par choisir une clé par défaut expirée. Le système de secours ne choisit jamais une clé révoquée comme clé par défaut, et si l’anneau de clé est vide ou si chaque clé a été révoquée, le système génère une erreur lors de l’initialisation.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Expiration et déroulement de la clé

Quand une clé est créée, elle reçoit automatiquement la date d’activation {Now + 2 days} et la date d’expiration {Now + 90 jours}. Le délai de 2 jours avant l’activation indique que la durée de la clé doit être retournée dans le système. Autrement dit, il permet à d’autres applications qui pointent dans le magasin de stockage d’observer la clé lors de leur prochaine période d’actualisation automatique, ce qui optimise les chances que lorsque l’anneau de clé devient actif, il est propagé à toutes les applications qui peuvent avoir besoin de l’utiliser.

Si la clé par défaut expire dans 2 jours et si l’anneau de clé n’a pas encore de clé qui sera active à l’expiration de la clé par défaut, le système de protection des données conserve automatiquement une nouvelle clé de l’anneau de clé. Cette nouvelle clé a une date d’activation de {date d’expiration de la clé par défaut} et une date d’expiration de {Now + 90 jours}. Cela permet au système de répercuter automatiquement les clés régulièrement sans interruption de service.

Dans certains cas, une clé est créée avec l’activation immédiate. Par exemple, si l’application n’est pas exécutée pendant une durée et que toutes les clés de l’anneau de clé ont expiré. Dans ce cas, la clé reçoit la date d’activation {Now} sans délai d’activation normal de 2 jours.

La durée de vie de la clé par défaut est de 90 jours, bien que cela soit configurable comme dans l’exemple suivant.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Un administrateur peut également modifier l’ensemble du système par défaut, bien qu’un appel explicite à `SetDefaultKeyLifetime` remplace toute stratégie à l’ensemble du système. La durée de vie de la clé par défaut ne peut pas être inférieure à 7 jours.

## <a name="automatic-key-ring-refresh"></a>Actualisation automatique des sonneries de touche

Lorsque le système de protection des données est initialisé, il lit l’anneau de clé à partir du référentiel sous-jacent et le met en cache dans la mémoire. Ce cache permet de procéder à des opérations de protection et d’annulation de la protection sans toucher au magasin de stockage. Le système recherche automatiquement les modifications dans le magasin de stockage environ toutes les 24 heures, ou à l’expiration de la clé par défaut actuelle.

>[!WARNING]
> Les développeurs doivent très rarement (voire jamais) utiliser directement les API de gestion de clés. Le système de protection des données effectuera la gestion automatique des clés comme décrit ci-dessus.

Le système de protection des données expose une interface `IKeyManager` qui peut être utilisée pour inspecter et apporter des modifications à l’anneau de clé. Le système DI qui a fourni l’instance de `IDataProtectionProvider` peut également fournir une instance de `IKeyManager` pour votre consommation. Vous pouvez également extraire `IKeyManager` directement du `IServiceProvider` comme dans l’exemple ci-dessous.

Toute opération qui modifie l’anneau de clé (création explicite d’une nouvelle clé ou exécution d’un révocation) invalidera le cache en mémoire. Le prochain appel à `Protect` ou `Unprotect` entraînera la relecture par le système de protection des données de l’anneau de clé et la recréation du cache.

L’exemple ci-dessous illustre l’utilisation `IKeyManager` de l’interface pour inspecter et manipuler l’anneau clé, y compris la révocation manuelle des clés existantes et la génération manuelle d’une nouvelle clé.

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>Stockage des clés

Le système de protection des données a un heuristique qui tente de déduire automatiquement un emplacement de stockage de clé approprié et un mécanisme de chiffrement au repos. Le mécanisme de persistance des clés est également configurable par le développeur de l’application. Les documents suivants décrivent les implémentations intégrées de ces mécanismes :

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
