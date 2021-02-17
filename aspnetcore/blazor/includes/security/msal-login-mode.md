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
ms.openlocfilehash: 1b045d437d1a16eabc0ab41573c8b66d9c4bb77e
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552790"
---
<span data-ttu-id="08441-101">L’infrastructure affiche par défaut le mode de connexion contextuelle et revient à rediriger le mode de connexion si une fenêtre contextuelle ne peut pas être ouverte.</span><span class="sxs-lookup"><span data-stu-id="08441-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="08441-102">Configurez MSAL pour utiliser le mode de connexion de redirection en affectant `LoginMode` à la propriété de la valeur <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `redirect` :</span><span class="sxs-lookup"><span data-stu-id="08441-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="08441-103">Le paramètre par défaut est `popup` , et la valeur de chaîne ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="08441-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
