L’infrastructure affiche par défaut le mode de connexion contextuelle et revient à rediriger le mode de connexion si une fenêtre contextuelle ne peut pas être ouverte. Configurez MSAL pour utiliser le mode de connexion de redirection en affectant `LoginMode` à la propriété de la valeur <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `redirect` :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

Le paramètre par défaut est `popup` , et la valeur de chaîne ne respecte pas la casse.
