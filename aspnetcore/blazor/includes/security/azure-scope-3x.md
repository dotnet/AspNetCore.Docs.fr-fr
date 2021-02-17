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
ms.openlocfilehash: ae5d8cf64c0db9402a5457cec1df8402f4db103c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552383"
---
<span data-ttu-id="d4d24-101">L’Portail Azure fournit l’un des formats d’URI d’ID d’application suivants selon que le locataire AAD a un [domaine d’éditeur vérifié ou non vérifié](/azure/active-directory/develop/howto-configure-publisher-domain):</span><span class="sxs-lookup"><span data-stu-id="d4d24-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="d4d24-102">Lorsque le deuxième des deux URI ID d’application précédents est utilisé dans l’application cliente pour former l’URI d’étendue et que l’autorisation n’est pas réussie, essayez de fournir l’URI d’étendue sans le schéma et l’hôte :</span><span class="sxs-lookup"><span data-stu-id="d4d24-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="d4d24-103">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d4d24-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
