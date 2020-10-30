---
title: Authentification à deux facteurs avec SMS dans ASP.NET Core
author: rick-anderson
description: Découvrez comment configurer l’authentification à deux facteurs (2FA) avec une application ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
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
uid: security/authentication/2fa
ms.openlocfilehash: 1ee9e656c2e631c9b5588149e0a75e07108baff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051263"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Authentification à deux facteurs avec SMS dans ASP.NET Core

Par [Rick Anderson](https://twitter.com/RickAndMSFT) et [Suisse-développeurs](https://github.com/Swiss-Devs)

>[!WARNING]
> Les applications de l’authentificateur 2FA (authentification à deux facteurs), utilisant un algorithme de mot de passe à usage unique (TOTP), sont l’approche recommandée pour 2FA. 2FA utilise TOTP est préférable à SMS 2FA. Pour plus d’informations, consultez [activer la génération de code QR pour les applications TOTP Authenticator dans ASP.net Core](xref:security/authentication/identity-enable-qrcodes) pour ASP.net Core 2,0 et versions ultérieures.

Ce didacticiel montre comment configurer l’authentification à deux facteurs (2FA) à l’aide de SMS. Des instructions sont fournies pour [Twilio](https://www.twilio.com/) et [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), mais vous pouvez utiliser n’importe quel autre fournisseur SMS. Nous vous recommandons de terminer la [confirmation du compte et la récupération du mot de passe avant de](xref:security/authentication/accconfirm) commencer ce didacticiel.

[Affichez ou téléchargez l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA). [Comment télécharger](xref:index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Créer un projet ASP.NET Core

Créez une nouvelle ASP.NET Core application Web nommée `Web2FA` avec des comptes d’utilisateur individuels. Suivez les instructions dans <xref:security/enforcing-ssl> pour configurer et exiger le protocole HTTPS.

### <a name="create-an-sms-account"></a>Créer un compte SMS

Créez un compte SMS, par exemple, à partir de [Twilio](https://www.twilio.com/) ou [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Enregistrez les informations d’authentification (pour Twilio : accountSid et authToken, pour ASPSMS : sensibles et Password).

#### <a name="figuring-out-sms-provider-credentials"></a>Identification des informations d’identification du fournisseur SMS

**Twilio**

À partir de l’onglet tableau de bord de votre compte Twilio, copiez le **SID de compte** et le **jeton d’authentification** .

**ASPSMS:**

Dans les paramètres de votre compte, accédez à **sensibles** et copiez-le avec votre **mot de passe** .

Nous stockerons ces valeurs ultérieurement dans avec l’outil secret-Manager dans les clés `SMSAccountIdentification` et `SMSAccountPassword` .

#### <a name="specifying-senderid--originator"></a>Spécification de SenderID/Originator

**Twilio :** Dans l’onglet nombres, copiez votre **numéro de téléphone** Twilio.

**ASPSMS :** Dans le menu déverrouiller les expéditeurs, déverrouillez un ou plusieurs expéditeurs ou choisissez un expéditeur alphanumérique (non pris en charge par tous les réseaux).

Nous stockerons cette valeur ultérieurement avec l’outil secret-Manager dans la clé `SMSAccountFrom` .

### <a name="provide-credentials-for-the-sms-service"></a>Fournir les informations d’identification du service SMS

Nous allons utiliser le [modèle d’options](xref:fundamentals/configuration/options) pour accéder au compte d’utilisateur et aux paramètres de clé.

* Créez une classe pour extraire la clé SMS sécurisée. Pour cet exemple, la `SMSoptions` classe est créée dans le fichier *services/SMSoptions. cs* .

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Définissez les `SMSAccountIdentification` `SMSAccountPassword` et `SMSAccountFrom` avec l' [outil de gestion de secret](xref:security/app-secrets). Exemple :

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* Ajoutez le package NuGet pour le fournisseur SMS. À partir de la console du gestionnaire de package (PMC), exécutez :

**Twilio**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* Ajoutez du code dans le fichier *services/MessageServices. cs* pour activer SMS. Utilisez la section Twilio ou ASPSMS :

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Configurer le démarrage à utiliser `SMSoptions`

Ajoutez `SMSoptions` au conteneur de service dans la `ConfigureServices` méthode de *Startup.cs* :

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Activer l’authentification à deux facteurs

Ouvrez le fichier de vue *views/Manage/index. cshtml* Razor et supprimez les caractères de commentaire (aucune balise n’est donc commentée).

## <a name="log-in-with-two-factor-authentication"></a>Se connecter avec l’authentification à deux facteurs

* Exécuter l’application et inscrire un nouvel utilisateur

![Vue du registre de l’application Web ouverte dans Microsoft Edge](2fa/_static/login2fa1.png)

* Appuyez sur votre nom d’utilisateur, ce qui active la `Index` méthode d’action dans gérer le contrôleur. Cliquez ensuite sur le lien numéro de téléphone **Ajouter** .

![Gérer l’affichage-Appuyez sur le lien « ajouter »](2fa/_static/login2fa2.png)

* Ajoutez un numéro de téléphone qui recevra le code de vérification, puis appuyez sur **Envoyer le code de vérification** .

![Page Ajouter un numéro de téléphone](2fa/_static/login2fa3.png)

* Vous recevrez un message texte contenant le code de vérification. Entrez-le et appuyez sur **Envoyer**

![Page vérifier le numéro de téléphone](2fa/_static/login2fa4.png)

Si vous n’obtenez pas de SMS, consultez la page du journal Twilio.

* La vue gérer indique que votre numéro de téléphone a été ajouté avec succès.

![Gérer la vue-le numéro de téléphone a été ajouté](2fa/_static/login2fa5.png)

* Appuyez sur **activer** pour activer l’authentification à deux facteurs.

![Gérer l’affichage-activer l’authentification à deux facteurs](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Tester l’authentification à deux facteurs

* Fermez la session.

* Connectez-vous.

* Le compte d’utilisateur a activé l’authentification à deux facteurs. vous devez donc fournir le deuxième facteur d’authentification. Dans ce didacticiel, vous avez activé la vérification par téléphone. Les modèles intégrés vous permettent également de configurer le courrier électronique comme second facteur. Vous pouvez configurer des facteurs secondaires supplémentaires pour l’authentification, tels que les codes QR. Appuyez sur **Envoyer** .

![Envoyer l’affichage du code de vérification](2fa/_static/login2fa7.png)

* Entrez le code que vous recevez dans le message SMS.

* Si vous cliquez sur la case à cocher **mémoriser ce navigateur** , vous ne devez pas utiliser 2FA pour vous connecter lorsque vous utilisez le même appareil et le même navigateur. L’activation de 2FA et le clic sur **mémoriser ce navigateur** vous offriront une protection 2FA renforcée contre les utilisateurs malveillants qui essaient d’accéder à votre compte, tant qu’ils n’ont pas accès à votre appareil. Vous pouvez le faire sur n’importe quel appareil privé que vous utilisez régulièrement. En définissant  **mémoriser ce navigateur** , vous bénéficiez de la sécurité supplémentaire de 2FA à partir des appareils que vous n’utilisez pas régulièrement, et vous avez l’avantage de ne pas devoir passer par 2FA sur vos propres appareils.

![Vérifier l’affichage](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Verrouillage de compte pour la protection contre les attaques par force brute

Le verrouillage de compte est recommandé avec 2FA. Une fois qu’un utilisateur se connecte par le biais d’un compte local ou d’un compte social, chaque tentative d’échec de 2FA est stockée. Si le nombre maximal de tentatives d’accès ayant échoué est atteint, l’utilisateur est verrouillé (valeur par défaut : 5 minutes de verrouillage après 5 tentatives d’accès ayant échoué). Une authentification réussie réinitialise le nombre d’échecs de tentatives d’accès et réinitialise l’horloge. Les tentatives d’accès et l’heure de verrouillage maximales peuvent être définies avec [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) et [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan). L’exemple suivant configure le verrouillage de compte pendant 10 minutes après 10 tentatives d’accès ayant échoué :

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Vérifiez que [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) définit `lockoutOnFailure` sur `true` :

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
