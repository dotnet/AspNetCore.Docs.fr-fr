<span data-ttu-id="c90b6-101">Le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ) est rendu dans le `MainLayout` composant ( `Shared/MainLayout.razor` ) et gère les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="c90b6-101">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="c90b6-102">Pour les utilisateurs authentifiés :</span><span class="sxs-lookup"><span data-stu-id="c90b6-102">For authenticated users:</span></span>
  * <span data-ttu-id="c90b6-103">Affiche le nom d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="c90b6-103">Displays the current username.</span></span>
  * <span data-ttu-id="c90b6-104">Offre un bouton permettant de se déconnecter de l’application.</span><span class="sxs-lookup"><span data-stu-id="c90b6-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="c90b6-105">Pour les utilisateurs anonymes, offre la possibilité de se connecter.</span><span class="sxs-lookup"><span data-stu-id="c90b6-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
