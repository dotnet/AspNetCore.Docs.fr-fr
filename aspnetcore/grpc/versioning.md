---
title: Contrôle de version des services gRPC
author: jamesnk
description: Découvrez comment effectuer la version des services gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 38204b16d041f21221862c566b90a6a9571d26a1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058699"
---
# <a name="versioning-grpc-services"></a>Contrôle de version des services gRPC

Par [James Newton-King](https://twitter.com/jamesnk)

Les nouvelles fonctionnalités ajoutées à une application peuvent nécessiter la modification des services gRPC fournis aux clients, parfois de façon inattendue et avec rupture. Quand les services gRPC changent :

* Il convient de tenir compte de l’impact des modifications sur les clients.
* Une stratégie de contrôle de version pour prendre en charge les modifications doit être implémentée.

## <a name="backwards-compatibility"></a>Compatibilité descendante

Le protocole gRPC est conçu pour prendre en charge les services qui changent au fil du temps. En règle générale, les ajouts aux services et méthodes gRPC sont sans rupture. Les modifications sans rupture permettent aux clients existants de continuer à fonctionner sans modification. La modification ou la suppression des services gRPC est une modification avec rupture. Lorsque les services gRPC comportent des modifications avec rupture, les clients qui utilisent ce service doivent être mis à jour et redéployés.

Apporter des modifications sans rupture à un service présente un certain nombre d’avantages :

* Les clients existants continuent à s’exécuter.
* Évite les tâches liées à la notification des clients des modifications avec rupture et à leur mise à jour.
* Une seule version du service doit être documentée et gérée.

### <a name="non-breaking-changes"></a>Changements non cassants

Ces modifications sont sans rupture au niveau du protocole gRPC et au niveau binaire .NET.

* **Ajout d’un nouveau service**
* **Ajout d’une nouvelle méthode à un service**
* **Ajout d’un champ à un message de demande** : les champs ajoutés à un message de demande sont désérialisés avec la [valeur par défaut](https://developers.google.com/protocol-buffers/docs/proto3#default) sur le serveur lorsqu’ils ne sont pas définis. Pour qu’il s’agit d’une modification sans rupture, le service doit être correctement exécuté lorsque le nouveau champ n’est pas défini par des clients plus anciens.
* **Ajout d’un champ à un message de réponse** : les champs ajoutés à un message de réponse sont désérialisés dans la collection de [champs inconnue](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) du message sur le client.
* **L’ajout d’une valeur à un enum** -enums est sérialisé en tant que valeur numérique. Les nouvelles valeurs enum sont désérialisées sur le client vers la valeur enum sans nom Enum. Pour qu’il s’agit d’une modification sans rupture, les anciens clients doivent s’exécuter correctement lors de la réception de la nouvelle valeur enum.

### <a name="binary-breaking-changes"></a>Modifications avec rupture binaire

Les modifications suivantes sont sans rupture au niveau du protocole gRPC, mais le client doit être mis à jour s’il est mis à niveau vers la *dernière version* du contrat ou de l’assembly .net du client. La compatibilité binaire est importante si vous envisagez de publier une bibliothèque gRPC dans NuGet.

* La **Suppression d’une** valeur de champ d’un champ supprimé est désérialisée dans les [champs inconnus](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)d’un message. Il ne s’agit pas d’une modification avec rupture de protocole gRPC, mais le client doit être mis à jour s’il est mis à niveau vers le dernier contrat. Il est important qu’un numéro de champ supprimé ne soit pas réutilisé par la suite. Pour vous assurer que cela ne se produit pas, spécifiez les numéros de champ et les noms supprimés dans le message à l’aide du mot clé [réservé](https://developers.google.com/protocol-buffers/docs/proto3#reserved) de Protobuf.
* Si vous **renommez un message** , les noms de message ne sont généralement pas envoyés sur le réseau. il ne s’agit donc pas d’une modification avec rupture de protocole gRPC. Le client devra être mis à jour s’il est mis à niveau vers le dernier contrat. Dans les cas où le nom du message est utilisé pour identifier le type de message, il peut [arriver que les](https://developers.google.com/protocol-buffers/docs/proto3#any) noms des messages **soient** envoyés sur le réseau.
* La modification **csharp_namespace** modification `csharp_namespace` de l’espace de noms des types .NET générés. Il ne s’agit pas d’une modification avec rupture de protocole gRPC, mais le client doit être mis à jour s’il est mis à niveau vers le dernier contrat.

### <a name="protocol-breaking-changes"></a>Modifications avec rupture de protocole

Les éléments suivants sont des modifications de protocole et de rupture binaire :

* Si vous **renommez un champ** avec du contenu Protobuf, les noms de champs sont utilisés uniquement dans le code généré. Le numéro de champ est utilisé pour identifier les champs sur le réseau. Le changement de nom d’un champ n’est pas une modification avec rupture de protocole pour Protobuf. Toutefois, si un serveur utilise du contenu JSON, le changement de nom d’un champ est une modification avec rupture.
* La **modification d’un** type de données de champ-la modification du type de données d’un champ en un [type incompatible](https://developers.google.com/protocol-buffers/docs/proto3#updating) provoquera des erreurs lors de la désérialisation du message. Même si le nouveau type de données est compatible, il est probable que le client doit être mis à jour pour prendre en charge le nouveau type s’il est mis à niveau vers le dernier contrat.
* La **modification d’un numéro de champ** avec des charges utiles Protobuf, le numéro de champ est utilisé pour identifier les champs sur le réseau.
* **Renommer un package, un service ou une méthode** -gRPC utilise le nom du package, le nom du service et le nom de la méthode pour générer l’URL. Le client obtient un État non *implémenté* à partir du serveur.
* **Suppression d’un service ou d’une méthode** : le client obtient un État non *implémenté* à partir du serveur lors de l’appel de la méthode supprimée.

### <a name="behavior-breaking-changes"></a>Modifications avec rupture de comportement

Lorsque vous apportez des modifications sans rupture, vous devez également déterminer si les clients plus anciens peuvent continuer à utiliser le nouveau comportement de service. Par exemple, l’ajout d’un nouveau champ à un message de demande :

* N’est pas une modification avec rupture de protocole.
* En retournant un état d’erreur sur le serveur si le nouveau champ n’est pas défini, il s’agit d’une modification avec rupture pour les anciens clients.

La compatibilité des comportements est déterminée par le code spécifique à votre application.

## <a name="version-number-services"></a>Services numéro de version

Les services doivent s’efforcer de conserver une compatibilité descendante avec les anciens clients. Enfin, les modifications apportées à votre application peuvent nécessiter des modifications avec rupture. Le fait de rompre les anciens clients et de les forcer à les mettre à jour en même temps que votre service n’est pas une bonne expérience utilisateur. Un moyen de maintenir la compatibilité descendante tout en effectuant des modifications avec rupture consiste à publier plusieurs versions d’un service.

gRPC prend en charge un spécificateur de [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) facultatif, qui fonctionne très bien comme un espace de noms .net. En fait, le `package` sera utilisé comme espace de noms .net pour les types .NET générés si `option csharp_namespace` n’est pas défini dans le fichier *. proto* . Le package peut être utilisé pour spécifier un numéro de version pour votre service et ses messages :

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Le nom du package est combiné avec le nom du service pour identifier une adresse de service. Une adresse de service autorise l’hébergement de plusieurs versions d’un service côte à côte :

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Les implémentations du service avec version sont inscrites dans *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

Le fait d’inclure un numéro de version dans le nom du package vous donne la possibilité de publier une version *v2* de votre service avec des modifications avec rupture, tout en continuant à prendre en charge les clients plus anciens qui appellent la version *v1* . Une fois que les clients ont été mis à jour pour utiliser le service *v2* , vous pouvez choisir de supprimer l’ancienne version. Lors de la planification de la publication de plusieurs versions d’un service :

* Évitez les modifications avec rupture si cela est raisonnable.
* Ne mettez pas à jour le numéro de version sauf si vous effectuez des modifications avec rupture.
* Mettez à jour le numéro de version lorsque vous apportez des modifications avec rupture.

La publication de plusieurs versions d’un service le duplique. Pour réduire la duplication, envisagez de déplacer la logique métier des implémentations de service vers un emplacement centralisé qui peut être réutilisé par les anciennes et nouvelles implémentations :

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Les services et les messages générés avec des noms de packages différents sont des **types .net différents**. Le déplacement d’une logique métier vers un emplacement centralisé requiert le mappage de messages à des types communs.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:grpc/protobuf>
