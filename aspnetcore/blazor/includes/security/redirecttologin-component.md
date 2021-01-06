Le `RedirectToLogin` composant ( `Shared/RedirectToLogin.razor` ) :

* Gère la redirection des utilisateurs non autorisés vers la page de connexion.
* Conserve l’URL actuelle à laquelle l’utilisateur tente d’accéder afin qu’il puisse être renvoyé à cette page si l’authentification réussit.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
