<span data-ttu-id="f2271-101">L’Portail Azure fournit l’un des formats d’URI d’ID d’application suivants selon que le locataire AAD a un [domaine d’éditeur vérifié ou non vérifié](/azure/active-directory/develop/howto-configure-publisher-domain):</span><span class="sxs-lookup"><span data-stu-id="f2271-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="f2271-102">Lorsque le deuxième des deux URI ID d’application précédents est utilisé dans l’application cliente pour former l’URI d’étendue et que l’autorisation n’est pas réussie, essayez de fournir l’URI d’étendue sans le schéma et l’hôte :</span><span class="sxs-lookup"><span data-stu-id="f2271-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="f2271-103">Exemple :</span><span class="sxs-lookup"><span data-stu-id="f2271-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
