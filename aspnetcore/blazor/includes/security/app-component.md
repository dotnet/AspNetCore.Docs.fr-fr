Le `App` composant ( `App.razor` ) est similaire au `App` composant trouvé dans les applications serveur éblouissantes :

* Le <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> composant gère l’exposition de au <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> reste de l’application.
* Le <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> composant permet de s’assurer que l’utilisateur actuel est autorisé à accéder à une page donnée ou à effectuer un rendu du `RedirectToLogin` composant.
* Le `RedirectToLogin` composant gère la redirection des utilisateurs non autorisés vers la page de connexion.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!include[](../prefer-exact-matches.md)]
