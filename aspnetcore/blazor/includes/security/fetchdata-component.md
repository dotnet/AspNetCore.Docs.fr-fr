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
ms.openlocfilehash: 8772f383a830936881564ca95a7f034ba08a5798
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552865"
---
Le `FetchData` composant montre comment :

* Approvisionner un jeton d’accès.
* Utilisez le jeton d’accès pour appeler une API de ressource protégée dans l’application *serveur* .

La [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indique au Blazor WebAssembly système d’autorisation que l’utilisateur doit être autorisé à accéder à ce composant. La présence de l’attribut dans l' `Client` application n’empêche pas l’appel de l’API sur le serveur sans les informations d’identification appropriées. L' `Server` application doit également utiliser `[Authorize]` sur les points de terminaison appropriés pour les protéger correctement.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> s’occupe de demander un jeton d’accès qui peut être ajouté à la demande d’appel de l’API. Si le jeton est mis en cache ou si le service est en mesure d’approvisionner un nouveau jeton d’accès sans intervention de l’utilisateur, la demande de jeton réussit. Dans le cas contraire, la demande de jeton échoue avec un <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> , qui est intercepté dans une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction.

Afin d’obtenir le jeton à inclure dans la demande, l’application doit vérifier que la demande a réussi en appelant [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) .

Si la demande a réussi, la variable de jeton est remplie avec le jeton d’accès. La <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> propriété du jeton expose la chaîne littérale à inclure dans l' `Authorization` en-tête de la demande.

Si la requête a échoué parce que le jeton n’a pas pu être approvisionné sans intervention de l’utilisateur, le résultat du jeton contient une URL de redirection. Si vous accédez à cette URL, l’utilisateur accède à la page de connexion et revient à la page active après une authentification réussie.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
