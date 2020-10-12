<span data-ttu-id="4fcda-101">Lorsque vous utilisez une API serveur inscrite auprès d’AAD et que l’inscription AAD de l’application se trouve dans un locataire qui s’appuie sur un [domaine d’éditeur non vérifié](/azure/active-directory/develop/howto-configure-publisher-domain), l’URI ID d’application de votre application API serveur n’est pas au `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` .</span><span class="sxs-lookup"><span data-stu-id="4fcda-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="4fcda-102">Si c’est le cas, l’étendue du jeton d’accès par défaut dans `Program.Main` ( `Program.cs` ) de l' *`Client`* application ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="4fcda-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="4fcda-103">Pour configurer l’application API serveur pour une audience correspondante, définissez `Audience` dans le fichier de paramètres de l' *`Server`* application API () de façon `appsettings.json` à ce qu’elle corresponde à l’audience de l’application fournie par l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="4fcda-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

<span data-ttu-id="4fcda-104">Dans la configuration précédente, la fin de la `Audience` valeur n’inclut **pas** l’étendue par défaut `/{DEFAULT SCOPE}` .</span><span class="sxs-lookup"><span data-stu-id="4fcda-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="4fcda-105">Exemple :</span><span class="sxs-lookup"><span data-stu-id="4fcda-105">Example:</span></span>

<span data-ttu-id="4fcda-106">`Program.Main` ( `Program.cs` ) de l' *`Client`* application :</span><span class="sxs-lookup"><span data-stu-id="4fcda-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="4fcda-107">Configurez le fichier de paramètres de l' *`Server`* application API ( `appsettings.json` ) avec un public correspondant ( `Audience` ) :</span><span class="sxs-lookup"><span data-stu-id="4fcda-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

<span data-ttu-id="4fcda-108">Dans l’exemple précédent, la fin de la `Audience` valeur n’inclut **pas** l’étendue par défaut `/API.Access` .</span><span class="sxs-lookup"><span data-stu-id="4fcda-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
