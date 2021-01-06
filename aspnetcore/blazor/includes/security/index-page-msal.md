La page index page ( `wwwroot/index.html` ) comprend un script qui définit le `AuthenticationService` en JavaScript. `AuthenticationService` gère les détails de bas niveau du protocole OIDC. L’application appelle en interne des méthodes définies dans le script pour effectuer les opérations d’authentification.

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
