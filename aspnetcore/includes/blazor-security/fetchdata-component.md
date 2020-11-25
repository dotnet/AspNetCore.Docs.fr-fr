<span data-ttu-id="f2c36-101">Le `FetchData` composant montre comment :</span><span class="sxs-lookup"><span data-stu-id="f2c36-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="f2c36-102">Approvisionner un jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="f2c36-102">Provision an access token.</span></span>
* <span data-ttu-id="f2c36-103">Utilisez le jeton d’accès pour appeler une API de ressource protégée dans l’application *serveur* .</span><span class="sxs-lookup"><span data-stu-id="f2c36-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="f2c36-104">La [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indique au système d’autorisation de Webassembly éblouissant que l’utilisateur doit être autorisé à accéder à ce composant.</span><span class="sxs-lookup"><span data-stu-id="f2c36-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="f2c36-105">La présence de l’attribut dans l' `Client` application n’empêche pas l’appel de l’API sur le serveur sans les informations d’identification appropriées.</span><span class="sxs-lookup"><span data-stu-id="f2c36-105">The presence of the attribute in the `Client` app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="f2c36-106">L' `Server` application doit également utiliser `[Authorize]` sur les points de terminaison appropriés pour les protéger correctement.</span><span class="sxs-lookup"><span data-stu-id="f2c36-106">The `Server` app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="f2c36-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> s’occupe de demander un jeton d’accès qui peut être ajouté à la demande d’appel de l’API.</span><span class="sxs-lookup"><span data-stu-id="f2c36-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="f2c36-108">Si le jeton est mis en cache ou si le service est en mesure d’approvisionner un nouveau jeton d’accès sans intervention de l’utilisateur, la demande de jeton réussit.</span><span class="sxs-lookup"><span data-stu-id="f2c36-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="f2c36-109">Dans le cas contraire, la demande de jeton échoue avec un <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> , qui est intercepté dans une [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instruction.</span><span class="sxs-lookup"><span data-stu-id="f2c36-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="f2c36-110">Afin d’obtenir le jeton à inclure dans la demande, l’application doit vérifier que la demande a réussi en appelant [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) .</span><span class="sxs-lookup"><span data-stu-id="f2c36-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="f2c36-111">Si la demande a réussi, la variable de jeton est remplie avec le jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="f2c36-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="f2c36-112">La <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> propriété du jeton expose la chaîne littérale à inclure dans l' `Authorization` en-tête de la demande.</span><span class="sxs-lookup"><span data-stu-id="f2c36-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="f2c36-113">Si la requête a échoué parce que le jeton n’a pas pu être approvisionné sans intervention de l’utilisateur, le résultat du jeton contient une URL de redirection.</span><span class="sxs-lookup"><span data-stu-id="f2c36-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="f2c36-114">Si vous accédez à cette URL, l’utilisateur accède à la page de connexion et revient à la page active après une authentification réussie.</span><span class="sxs-lookup"><span data-stu-id="f2c36-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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
