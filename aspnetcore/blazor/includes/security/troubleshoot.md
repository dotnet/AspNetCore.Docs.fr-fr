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
ms.openlocfilehash: 4e7c0e9b0a164e0181af5d6baaedf0669c1c06aa
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551662"
---
## <a name="troubleshoot"></a>Dépanner

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a>Erreurs courantes

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

### <a name="run-the-server-app"></a>Exécuter l’application serveur

Lors du test et du dépannage d’une application hébergée, assurez- Blazor vous que vous exécutez l’application à partir du **`Server`** projet. Par exemple, dans Visual Studio, vérifiez que le projet serveur est mis en surbrillance dans **Explorateur de solutions** avant de démarrer l’application avec l’une des approches suivantes :

* Sélectionnez le bouton **Run**.
* Utilisez **débogage**  >  **Démarrer le débogage** à partir du menu.
* Appuyez sur <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Inspecter le contenu d’un JSON Web Token (JWT)

Pour décoder un JSON Web Token (JWT), utilisez l’outil [JWT.ms](https://jwt.ms/) de Microsoft. Les valeurs de l’interface utilisateur ne laissent jamais votre navigateur.
