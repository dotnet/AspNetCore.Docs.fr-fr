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
ms.openlocfilehash: 5964554c36e2242b70faee390374828acd2bd860
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552306"
---
Lorsque vous utilisez une API serveur inscrite auprès d’AAD et que l’inscription AAD de l’application se trouve dans un locataire qui s’appuie sur un [domaine d’éditeur non vérifié](/azure/active-directory/develop/howto-configure-publisher-domain), l’URI ID d’application de votre application API serveur n’est pas au `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` . Si c’est le cas, l’étendue du jeton d’accès par défaut dans `Program.Main` ( `Program.cs` ) de l' *`Client`* application ressemble à ce qui suit :

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

Pour configurer l’application API serveur pour une audience correspondante, définissez `Audience` dans le fichier de paramètres de l' *`Server`* application API () de façon `appsettings.json` à ce qu’elle corresponde à l’audience de l’application fournie par l’portail Azure :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

Dans la configuration précédente, la fin de la `Audience` valeur n’inclut **pas** l’étendue par défaut `/{DEFAULT SCOPE}` .

Exemple :

`Program.Main` ( `Program.cs` ) de l' *`Client`* application :

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

Configurez le fichier de paramètres de l' *`Server`* application API ( `appsettings.json` ) avec un public correspondant ( `Audience` ) :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

Dans l’exemple précédent, la fin de la `Audience` valeur n’inclut **pas** l’étendue par défaut `/API.Access` .
