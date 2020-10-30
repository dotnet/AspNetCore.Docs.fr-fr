---
title: Configuration de la connexion externe Twitter avec ASP.NET Core
author: rick-anderson
description: Ce didacticiel illustre l’intégration de l’authentification utilisateur de compte Twitter dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: 47926d12ac5f922f2937df164d38ff6eb63cacf1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053278"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Configuration de la connexion externe Twitter avec ASP.NET Core

Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Cet exemple montre comment permettre aux utilisateurs de [se connecter avec leur compte Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) à l’aide d’un exemple de projet ASP.net Core 3,0 créé sur la [page précédente](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Créer l’application dans Twitter

* Ajoutez le package NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) au projet.

* Accédez à [https://apps.twitter.com/](https://apps.twitter.com/) et connectez-vous. Si vous ne disposez pas déjà d’un compte Twitter, utilisez le lien **[Inscrivez-vous maintenant](https://twitter.com/signup)** pour en créer un.

* Sélectionnez **créer une application** . Renseignez le **nom** de l' **application** , la description de l’application et l’URI du **site Web** public (cela peut être temporaire jusqu’à ce que vous enregistriez le nom de domaine) :

* Cochez la case en regard de **activer la connexion avec Twitter** .

* Microsoft. AspNetCore.Identity requiert que les utilisateurs aient une adresse de messagerie par défaut. Accédez à l’onglet **autorisations** , cliquez sur le bouton **modifier** et cochez la case en regard de **demander l’adresse de messagerie des utilisateurs** .

* Entrez votre URI de développement avec `/signin-twitter` ajouté dans le champ **URL de rappel** (par exemple : `https://webapp128.azurewebsites.net/signin-twitter` ). Le schéma d’authentification Twitter configuré plus tard dans cet exemple gère automatiquement les demandes à `/signin-twitter` l’itinéraire pour implémenter le Flow OAuth.

  > [!NOTE]
  > Le segment d’URI `/signin-twitter` est défini en tant que rappel par défaut du fournisseur d’authentification Twitter. Vous pouvez modifier l’URI de rappel par défaut lors de la configuration de l’intergiciel d’authentification Twitter via la propriété héritée [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la classe [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .

* Remplissez le reste du formulaire et sélectionnez **créer** . Les détails de la nouvelle application s’affichent :

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Stocker la clé et le secret de l’API du consommateur Twitter

Stockez les paramètres sensibles, tels que la clé de l’API du consommateur Twitter et le secret avec le [Gestionnaire de secret](xref:security/app-secrets). Pour cet exemple, procédez comme suit :

1. Initialisez le projet pour le stockage secret conformément aux instructions de la procédure [activer le stockage secret](xref:security/app-secrets#enable-secret-storage).
1. Stockez les paramètres sensibles dans le magasin de secret local avec les clés secrètes `Authentication:Twitter:ConsumerKey` et `Authentication:Twitter:ConsumerSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Ces jetons se trouvent sous l’onglet **clés et jetons d’accès** après la création d’une application Twitter :

## <a name="configure-twitter-authentication"></a>Configurer l’authentification Twitter

Ajoutez le service Twitter dans la `ConfigureServices` méthode dans le fichier *Startup.cs* :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Pour plus d’informations sur les options de configuration prises en charge par l’authentification Twitter, consultez la référence de l’API [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) . Cela peut être utilisé pour demander différentes informations sur l’utilisateur.

## <a name="sign-in-with-twitter"></a>Se connecter avec Twitter

Exécutez l’application et sélectionnez **se connecter** . Une option permettant de se connecter avec Twitter s’affiche :

En cliquant sur **Twitter** , vous redirigez vers Twitter pour l’authentification :

Après avoir entré vos informations d’identification Twitter, vous êtes redirigé vers le site Web où vous pouvez définir votre adresse de messagerie.

Vous êtes maintenant connecté à l’aide de vos informations d’identification Twitter :

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Dépannage

* **ASP.net Core 2. x uniquement :** Si Identity n’est pas configuré en appelant `services.AddIdentity` dans `ConfigureServices` , toute tentative d’authentification entraîne une *exception ArgumentException : l’option « SignInScheme » doit être fournie* . Le modèle de projet utilisé dans cet exemple permet de s’assurer que cette opération est effectuée.
* Si la base de données de site n’a pas été créée en appliquant la migration initiale, vous obtiendrez *une opération de base de données qui a échoué lors du traitement de l’erreur de demande* . Appuyez sur **appliquer des migrations** pour créer la base de données, puis sur Actualiser pour poursuivre l’erreur.

## <a name="next-steps"></a>Étapes suivantes

* Cet article vous a montré comment vous pouvez vous authentifier avec Twitter. Vous pouvez suivre une approche similaire pour vous authentifier auprès d’autres fournisseurs listés dans la [page précédente](xref:security/authentication/social/index).

* Une fois que vous publiez votre site Web sur Azure Web App, vous devez réinitialiser le `ConsumerSecret` dans le portail des développeurs Twitter.

* Définissez les `Authentication:Twitter:ConsumerKey` et `Authentication:Twitter:ConsumerSecret` en tant que paramètres d’application dans la portail Azure. Le système de configuration est configuré pour lire des clés à partir de variables d’environnement.
