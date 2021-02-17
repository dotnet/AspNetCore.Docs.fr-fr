---
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
ms.openlocfilehash: 5ff4e4368d9e6d7c8525ae4ef0625d176a256a85
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551519"
---
Blazor applications serveur en direct dans la mémoire du serveur. Cela signifie qu’il existe plusieurs applications hébergées dans le même processus. Pour chaque session d’application, Blazor démarre un circuit avec sa propre étendue d’injection de conteneurs. Cela signifie que les services délimités sont uniques par Blazor session.

> [!WARNING]
> Nous vous déconseillons d’utiliser les applications sur le même état de partage de serveur à l’aide des services Singleton, sauf si une extrême prudence est prise, car cela peut introduire des failles de sécurité, telles que la fuite de l’état utilisateur entre les circuits.

Vous pouvez utiliser des services Singleton avec état dans Blazor les applications s’ils sont spécifiquement conçus pour celui-ci. Par exemple, il est possible d’utiliser un cache mémoire en tant que singleton, car il requiert une clé pour accéder à une entrée donnée, en supposant que les utilisateurs n’ont pas le contrôle des clés de cache utilisées.

**De plus, pour des raisons de sécurité, vous ne devez pas utiliser <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> dans les Blazor applications.** Blazor les applications s’exécutent en dehors du contexte du pipeline ASP.NET Core. Le <xref:Microsoft.AspNetCore.Http.HttpContext> n’est pas forcément disponible dans le <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> , et il n’est pas garanti qu’il détient le contexte qui a démarré l' Blazor application.

La méthode recommandée pour passer l’état de la demande à l' Blazor application consiste à utiliser des paramètres du composant racine dans le rendu initial de l’application :

* Définissez une classe avec toutes les données que vous souhaitez transmettre à l' Blazor application.
* Renseignez ces données à partir de la Razor page en utilisant le <xref:Microsoft.AspNetCore.Http.HttpContext> disponible à ce moment-là.
* Transmettez les données à l' Blazor application en tant que paramètre au composant racine (application).
* Définissez un paramètre dans le composant racine pour stocker les données passées à l’application.
* Utilisez les données spécifiques à l’utilisateur dans l’application. vous pouvez également copier ces données dans un service étendu dans <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> afin qu’elles puissent être utilisées dans l’application.

Pour obtenir des informations supplémentaires ainsi qu'un code d'exemple, consultez <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.
