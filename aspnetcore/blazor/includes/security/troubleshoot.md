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
ms.openlocfilehash: f58da78475d65cbb70b0b177e1b0443535e97d55
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102402310"
---
## <a name="troubleshoot"></a>Dépanner

### <a name="common-errors"></a>Erreurs courantes

* Inconfiguration de l’application ou du Identity fournisseur (IP)

  Les erreurs les plus courantes sont provoquées par une configuration incorrecte. Voici quelques exemples :
  
  * Selon les exigences du scénario, une autorité, une instance, un ID de locataire, un domaine de locataire, un ID de client ou un URI de redirection manquant ou incorrect empêche une application d’authentifier des clients.
  * Une étendue de jeton d’accès incorrecte empêche les clients d’accéder aux points de terminaison de l’API Web du serveur.
  * Les autorisations d’API serveur incorrectes ou manquantes empêchent les clients d’accéder aux points de terminaison de l’API Web du serveur.
  * Exécution de l’application sur un port différent de celui configuré dans l’URI de redirection de l’inscription de l' Identity application du fournisseur.
  
  Les sections de configuration de cet article montrent des exemples de configuration correcte. Vérifiez attentivement chaque section de l’article à la recherche de la configuration de l’application et de l’adresse IP.
  
  Si la configuration semble correcte :
  
  * Analyser les journaux des applications.
  * Examinez le trafic réseau entre l’application cliente et l’application IP ou serveur avec les outils de développement du navigateur. Souvent, un message d’erreur exact ou un message indiquant ce qui est à l’origine du problème est renvoyé au client par l’application IP ou serveur après avoir effectué une demande. Des conseils sur les outils de développement sont disponibles dans les articles suivants :

    * [Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (documentation Google)
    * [Microsoft Edge](/microsoft-edge/devtools-guide-chromium/network/)
    * [Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (documentation Mozilla)

  * Décoder le contenu d’un JSON Web Token (JWT) utilisé pour l’authentification d’un client ou l’accès à une API Web de serveur, en fonction de l’endroit où le problème se produit. Pour plus d’informations, consultez [inspecter le contenu d’un JSON Web Token (JWT)](#inspect-the-content-of-a-json-web-token-jwt).
  
  L’équipe documentation répond aux commentaires sur les documents et aux bogues dans les articles (en ouvrant un problème de la section commentaires sur **cette page** ), mais ne peut pas fournir de support technique. Plusieurs forums de support publics sont disponibles pour vous aider à résoudre les problèmes d’une application. Nous recommandons ce qui suit :
  
  * [Stack Overflow (étiquette : `blazor` )](https://stackoverflow.com/questions/tagged/blazor)
  * [Équipe de marge ASP.NET Core](http://tattoocoder.com/aspnet-slack-sign-up/)
  * [Blazor Gitter](https://gitter.im/aspnet/Blazor)
  
  Pour les rapports de bogues d’infrastructure reproductibles non-sécurité, non sensibles et non confidentiels, [ouvrez un problème avec l’unité de produit ASP.net Core](https://github.com/dotnet/aspnetcore/issues). N’ouvrez pas de problème avec l’unité de produit tant que vous n’avez pas soigneusement étudié la cause d’un problème et que vous ne pouvez pas le résoudre par vous-même et avec l’aide de la communauté sur un forum de support public. L’unité de produit n’est pas en mesure de dépanner des applications individuelles endommagées en raison d’une configuration simple ou de cas d’utilisation impliquant des services tiers. Si un rapport est sensible ou confidentiel par nature ou décrit une faille de sécurité potentielle dans le produit que des attaquants peuvent exploiter, consultez Rapports sur les [problèmes de sécurité et les bogues (référentiel GitHub dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs).

::: moniker range=">= aspnetcore-5.0"

* Client non autorisé pour AAD

  > info : échec de l’autorisation Microsoft. AspNetCore. Authorization. DefaultAuthorizationService [2]. Ces exigences n’ont pas été satisfaites : DenyAnonymousAuthorizationRequirement : requiert un utilisateur authentifié.

  Erreur de rappel de connexion à partir d’AAD :

  * Erreur : `unauthorized_client`
  * Description : `AADB2C90058: The provided application is not configured to allow public clients.`

  Pour résoudre l’erreur :

  1. Dans le Portail Azure, accédez au [manifeste de l’application](/azure/active-directory/develop/reference-app-manifest).
  1. Affectez à l' [ `allowPublicClient` attribut](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) la valeur `null` ou `true` .

::: moniker-end

### <a name="cookies-and-site-data"></a>Cookies et données de site

Cookieles s et les données de site peuvent être conservées entre les mises à jour d’application et interfèrent avec les tests et la résolution des problèmes. Désactivez les éléments suivants lorsque vous apportez des modifications de code d’application, des modifications de compte d’utilisateur avec le fournisseur ou des modifications de configuration d’application de fournisseur :

* Connexion cookie de l’utilisateur
* Application cookie s
* Données de site mises en cache et stockées

L’une des méthodes permettant d’empêcher les éléments en attente cookie et les données de site d’interférer avec les tests et la résolution des problèmes consiste à :

* Configurer un navigateur
  * Utilisez un navigateur pour le test que vous pouvez configurer pour supprimer toutes les cookie données de site et chaque fois que le navigateur est fermé.
  * Assurez-vous que le navigateur est fermé manuellement ou par l’IDE pour toute modification apportée à l’application, à l’utilisateur de test ou à la configuration du fournisseur.
* Utilisez une commande personnalisée pour ouvrir un navigateur en mode Incognito ou privé dans Visual Studio :
  * Ouvrez la boîte de dialogue **naviguer avec** à partir du bouton **exécuter** de Visual Studio.
  * Sélectionnez le bouton **Ajouter**.
  * Indiquez le chemin d’accès à votre navigateur dans le champ **programme** . Les chemins d’accès exécutables suivants sont des emplacements d’installation typiques pour Windows 10. Si votre navigateur est installé à un autre emplacement ou si vous n’utilisez pas Windows 10, indiquez le chemin d’accès à l’exécutable du navigateur.
    * Microsoft Edge : `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome : `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox : `C:\Program Files\Mozilla Firefox\firefox.exe`
  * Dans le champ **arguments** , indiquez l’option de ligne de commande utilisée par le navigateur pour ouvrir en mode Incognito ou privé. Certains navigateurs requièrent l’URL de l’application.
    * Microsoft Edge : utilisez `-inprivate` .
    * Google Chrome : utilisez `--incognito --new-window {URL}` , où l’espace réservé `{URL}` est l’URL à ouvrir (par exemple, `https://localhost:5001` ).
    * Mozilla Firefox : utilisez `-private -url {URL}` , où l’espace réservé `{URL}` est l’URL à ouvrir (par exemple, `https://localhost:5001` ).
  * Entrez un nom dans le champ **nom convivial** . Par exemple : `Firefox Auth Testing`.
  * Cliquez sur le bouton **OK**.
  * Pour éviter d’avoir à sélectionner le profil de navigateur pour chaque itération de test avec une application, définissez le profil par défaut avec le bouton **définir comme valeur par défaut** .
  * Assurez-vous que le navigateur est fermé par l’IDE pour toute modification apportée à l’application, à l’utilisateur de test ou à la configuration du fournisseur.

### <a name="app-upgrades"></a>Mise à niveau d’applications

Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :

1. Effacez les caches de package NuGet du système local en exécutant [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) à partir d’une interface de commande.
1. Supprimez les `bin` dossiers et du projet `obj` .
1. Restaurez et regénérez le projet.
1. Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.

> [!NOTE]
> L’utilisation de versions de package incompatibles avec le Framework cible de l’application n’est pas prise en charge. Pour plus d’informations sur un package, utilisez la [galerie NuGet](https://www.nuget.org) ou l' [Explorateur de packages FuGet](https://www.fuget.org).

### <a name="run-the-server-app"></a>Exécuter l’application serveur

Lors du test et du dépannage d’une solution hébergée, assurez- Blazor vous que vous exécutez l’application à partir du **`Server`** projet. Par exemple, dans Visual Studio, vérifiez que le projet serveur est mis en surbrillance dans **Explorateur de solutions** avant de démarrer l’application avec l’une des approches suivantes :

* Sélectionnez le bouton **Run**.
* Utilisez **débogage**  >  **Démarrer le débogage** à partir du menu.
* Appuyez sur <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Inspecter le contenu d’un JSON Web Token (JWT)

Pour décoder un JSON Web Token (JWT), utilisez l’outil [JWT.ms](https://jwt.ms/) de Microsoft. Les valeurs de l’interface utilisateur ne laissent jamais votre navigateur.

Exemple de JWT encodé (raccourci pour l’affichage) :

> eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q

Exemple JWT décodé par l’outil pour une application qui s’authentifie auprès d’Azure AAD B2C :

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1610059429,
  "nbf": 1610055829,
  "ver": "1.0",
  "iss": "https://mysiteb2c.b2clogin.com/5cc15ea8-a296-4aa3-97e4-226dcc9ad298/v2.0/",
  "sub": "5ee963fb-24d6-4d72-a1b6-889c6e2c7438",
  "aud": "70bde375-fce3-4b82-984a-b247d823a03f",
  "nonce": "b2641f54-8dc4-42ca-97ea-7f12ff4af871",
  "iat": 1610055829,
  "auth_time": 1610055822,
  "idp": "idp.com",
  "tfp": "B2C_1_signupsignin"
}.[Signature]
```
