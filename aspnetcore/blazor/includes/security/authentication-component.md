La page produite par le `Authentication` composant ( `Pages/Authentication.razor` ) définit les itinéraires requis pour la gestion des différentes étapes d’authentification.

Le <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> composant :

* Est fourni par le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) Package.
* Gère l’exécution des actions appropriées à chaque étape de l’authentification.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
