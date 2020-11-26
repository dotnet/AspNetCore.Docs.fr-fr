<span data-ttu-id="987b3-101">Le `RedirectToLogin` composant ( `Shared/RedirectToLogin.razor` ) :</span><span class="sxs-lookup"><span data-stu-id="987b3-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="987b3-102">Gère la redirection des utilisateurs non autorisés vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="987b3-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="987b3-103">Conserve l’URL actuelle à laquelle l’utilisateur tente d’accéder afin qu’il puisse être renvoyé à cette page si l’authentification réussit.</span><span class="sxs-lookup"><span data-stu-id="987b3-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

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
