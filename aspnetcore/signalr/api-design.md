---
title: SignalR Considérations relatives à la conception d’API
author: anurse
description: Découvrez comment concevoir des SignalR API pour la compatibilité entre les versions de votre application.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: 87665a7950edbc70b664230d2f078598e9dbc0aa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059648"
---
# <a name="no-locsignalr-api-design-considerations"></a>SignalR Considérations relatives à la conception d’API

Par [Andrew Stanton-infirmière](https://twitter.com/anurse)

Cet article fournit des conseils sur la création d' SignalR API basées sur.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Utiliser des paramètres d’objet personnalisés pour garantir la compatibilité descendante

L’ajout de paramètres à une SignalR méthode de concentrateur (sur le client ou le serveur) est une *modification avec rupture* . Cela signifie que les clients/serveurs plus anciens recevront des erreurs lorsqu’ils essaieront d’appeler la méthode sans le nombre approprié de paramètres. Toutefois, l’ajout de propriétés à un paramètre d’objet personnalisé n’est **pas** une modification avec rupture. Cela peut être utilisé pour concevoir des API compatibles qui résistent aux modifications sur le client ou le serveur.

Par exemple, considérez une API côté serveur comme suit :

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

Le client JavaScript appelle cette méthode à l’aide `invoke` de comme suit :

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Si vous ajoutez ultérieurement un deuxième paramètre à la méthode serveur, les clients plus anciens ne fourniront pas cette valeur de paramètre. Exemple :

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Lorsque l’ancien client tente d’appeler cette méthode, il obtient une erreur semblable à celle-ci :

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

Sur le serveur, un message de journal semblable à celui-ci s’affiche :

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

L’ancien client n’a envoyé qu’un seul paramètre, mais l’API serveur la plus récente nécessitait deux paramètres. L’utilisation d’objets personnalisés comme paramètres vous offre plus de souplesse. Nous allons modifier la conception de l’API d’origine pour utiliser un objet personnalisé :

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

À présent, le client utilise un objet pour appeler la méthode :

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Au lieu d’ajouter un paramètre, ajoutez une propriété à l' `TotalLengthRequest` objet :

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Lorsque l’ancien client envoie un seul paramètre, la `Param2` propriété supplémentaire est conservée `null` . Vous pouvez détecter un message envoyé par un ancien client en activant la `Param2` pour `null` et en appliquant une valeur par défaut. Un nouveau client peut envoyer les deux paramètres.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

La même technique fonctionne pour les méthodes définies sur le client. Vous pouvez envoyer un objet personnalisé du côté serveur :

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

Côté client, vous accédez à la `Message` propriété au lieu d’utiliser un paramètre :

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Si vous décidez par la suite d’ajouter l’expéditeur du message à la charge utile, ajoutez une propriété à l’objet :

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Les clients plus anciens n’attendent pas la `Sender` valeur, donc ils l’ignorent. Un nouveau client peut l’accepter en mettant à jour pour lire la nouvelle propriété :

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

Dans ce cas, le nouveau client tolère également un ancien serveur qui ne fournit pas la `Sender` valeur. Étant donné que l’ancien serveur ne fournit pas la `Sender` valeur, le client vérifie s’il existe avant d’y accéder.
