---
title: Configuration de la connexion à Google External dans ASP.NET Core
author: rick-anderson
description: Ce didacticiel illustre l’intégration de l’authentification utilisateur de compte Google dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/18/2021
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
uid: security/authentication/google-logins
ms.openlocfilehash: 181ce87e8085839e0fcc0d77010c588ef7a290b1
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110129"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Configuration de la connexion à Google External dans ASP.NET Core

Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)

Ce didacticiel vous montre comment permettre aux utilisateurs de se connecter avec leur compte Google à l’aide du projet ASP.NET Core 3,0 créé sur la [page précédente](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Créer un projet de console d’API Google et un ID client

* Ajoutez le package NuGet [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) à l’application.
* Suivez les instructions de la section [intégration de google Sign-In à votre application Web](https://developers.google.com/identity/sign-in/web/sign-in) (documentation Google).
* Dans la page **informations d’identification** de la [console Google](https://console.developers.google.com/apis/credentials), sélectionnez créer des **informations d’identification**  >  **client OAuth ID**.
* Dans la boîte de dialogue **type d’application** , sélectionnez **application Web**. Fournissez un **nom** pour l’application.
* Dans la section **URI de redirection autorisés** , sélectionnez **Ajouter un URI** pour définir l’URI de redirection. Exemple d’URI de redirection : `https://localhost:{PORT}/signin-google` , où l' `{PORT}` espace réservé est le port de l’application.
* Sélectionnez le bouton **créer** .
* Enregistrez l' **ID client** et la **clé secrète client** pour les utiliser dans la configuration de l’application.
* Lors du déploiement du site, vous pouvez :
  * Mettez à jour l’URI de redirection de l’application dans la **console Google** vers l’URI de redirection déployée de l’application.
  * Créez une nouvelle inscription d’API Google dans la **console Google** pour l’application de production avec son URI de redirection de production.

## <a name="store-the-google-client-id-and-secret"></a>Stocker l’ID et le secret du client Google

Stockez les paramètres sensibles tels que l’ID client Google et les valeurs secrètes avec le [Gestionnaire de secret](xref:security/app-secrets). Pour cet exemple, procédez comme suit :

1. Initialisez le projet pour le stockage secret conformément aux instructions de la procédure [activer le stockage secret](xref:security/app-secrets#enable-secret-storage).
1. Stockez les paramètres sensibles dans le magasin de secret local avec les clés secrètes `Authentication:Google:ClientId` et `Authentication:Google:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Vous pouvez gérer les informations d’identification et l’utilisation de votre API dans la [console d’API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Configurer l’authentification Google

Ajoutez le service Google à `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Se connecter avec Google

* Exécutez l’application, puis cliquez sur **se connecter**. Une option de connexion avec Google s’affiche.
* Cliquez sur le bouton **Google** , qui redirige vers Google pour l’authentification.
* Après avoir entré vos informations d’identification Google, vous êtes redirigé vers le site Web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Pour plus d’informations sur les options de configuration prises en charge par l’authentification Google, consultez la référence de l’API. Cela peut être utilisé pour demander différentes informations sur l’utilisateur.

## <a name="change-the-default-callback-uri"></a>Modifier l’URI de rappel par défaut

Le segment `/signin-google` d’URI est défini en tant que rappel par défaut du fournisseur d’authentification Google. Vous pouvez modifier l’URI de rappel par défaut lors de la configuration de l’intergiciel d’authentification Google via la <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType> propriété Inherited) de la <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> classe.

## <a name="troubleshooting"></a>Dépannage

* Si la connexion ne fonctionne pas et que vous ne recevez pas d’erreurs, passez en mode développement pour faciliter le débogage du problème.
* Si Identity n’est pas configuré en appelant `services.AddIdentity` dans `ConfigureServices` , toute tentative d’authentification des résultats dans *ArgumentException : l’option « SignInScheme » doit être fournie*. Le modèle de projet utilisé dans ce didacticiel permet d’effectuer cette opération.
* Si la base de données de site n’a pas été créée en appliquant la migration initiale, vous recevez *une opération de base de données qui a échoué lors du traitement de l’erreur de demande* . Sélectionnez **appliquer les migrations** pour créer la base de données, puis actualisez la page pour poursuivre l’erreur.

## <a name="next-steps"></a>Étapes suivantes

* Cet article vous a montré comment vous pouvez vous authentifier auprès de Google. Vous pouvez suivre une approche similaire pour vous authentifier auprès d’autres fournisseurs listés dans la [page précédente](xref:security/authentication/social/index).
* Une fois que vous avez publié l’application dans Azure, réinitialisez la `ClientSecret` dans la console de l’API Google.
* Définissez les `Authentication:Google:ClientId` et `Authentication:Google:ClientSecret` en tant que paramètres d’application dans la portail Azure. Le système de configuration est configuré pour lire des clés à partir de variables d’environnement.
