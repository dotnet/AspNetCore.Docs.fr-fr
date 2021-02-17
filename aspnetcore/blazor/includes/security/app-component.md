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
ms.openlocfilehash: d632ab0604f81f7b6067d4535b0f5da0afe2e0ad
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551834"
---
<span data-ttu-id="559b6-101">Le `App` composant ( `App.razor` ) est similaire au `App` composant trouvé dans Blazor Server applications :</span><span class="sxs-lookup"><span data-stu-id="559b6-101">The `App` component (`App.razor`) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="559b6-102">Le <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> composant gère l’exposition de au <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> reste de l’application.</span><span class="sxs-lookup"><span data-stu-id="559b6-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="559b6-103">Le <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> composant permet de s’assurer que l’utilisateur actuel est autorisé à accéder à une page donnée ou à effectuer un rendu du `RedirectToLogin` composant.</span><span class="sxs-lookup"><span data-stu-id="559b6-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="559b6-104">Le `RedirectToLogin` composant gère la redirection des utilisateurs non autorisés vers la page de connexion.</span><span class="sxs-lookup"><span data-stu-id="559b6-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
