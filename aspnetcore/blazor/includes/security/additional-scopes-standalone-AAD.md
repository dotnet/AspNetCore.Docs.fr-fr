Ajoutez un <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> pour l' `User.Read` autorisation pour <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
