L’Portail Azure fournit l’un des formats d’URI d’ID d’application suivants selon que le locataire AAD a un [domaine d’éditeur vérifié ou non vérifié](/azure/active-directory/develop/howto-configure-publisher-domain):

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

Lorsque le deuxième des deux URI ID d’application précédents est utilisé dans l’application cliente pour former l’URI d’étendue et que l’autorisation n’est pas réussie, essayez de fournir l’URI d’étendue sans le schéma et l’hôte :

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

Exemple :

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
