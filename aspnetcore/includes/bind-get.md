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
ms.openlocfilehash: c6880852f63b1e91789667506f01680608933226
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552637"
---
> [!WARNING]
> Pour des raisons de sécurité, vous devez choisir de lier les données de requête `GET` aux propriétés du modèle de page. Vérifiez l’entrée utilisateur avant de la mapper à des propriétés. L’utilisation `GET` de la liaison est utile lors de l’adressage de scénarios qui reposent sur une chaîne de requête ou des valeurs de route.
>
> Pour lier une propriété à des `GET` demandes, affectez [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) à la propriété de l’attribut la valeur `SupportsGet` `true` :
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> Pour plus d’informations, consultez [ASP.net Core de la communauté réunions : lier sur obtenir une discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).
