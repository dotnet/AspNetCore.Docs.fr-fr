<span data-ttu-id="f01c3-101">L’infrastructure affiche par défaut le mode de connexion contextuelle et revient à rediriger le mode de connexion si une fenêtre contextuelle ne peut pas être ouverte.</span><span class="sxs-lookup"><span data-stu-id="f01c3-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="f01c3-102">Configurez MSAL pour utiliser le mode de connexion de redirection en affectant `LoginMode` à la propriété de la valeur <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `redirect` :</span><span class="sxs-lookup"><span data-stu-id="f01c3-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="f01c3-103">Le paramètre par défaut est `popup` , et la valeur de chaîne ne respecte pas la casse.</span><span class="sxs-lookup"><span data-stu-id="f01c3-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
