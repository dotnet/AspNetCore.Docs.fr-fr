---
title: Authentifier les utilisateurs avec WS-Federation dans ASP.NET Core
author: chlowell
description: Ce didacticiel montre comment utiliser WS-Federation dans une application ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
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
uid: security/authentication/ws-federation
ms.openlocfilehash: ed78923a2bdd1ed683a72c0a6f34337a38350035
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053369"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Authentifier les utilisateurs avec WS-Federation dans ASP.NET Core

Ce didacticiel montre comment permettre aux utilisateurs de se connecter avec un fournisseur d’authentification WS-Federation comme Services ADFS (ADFS) ou [Azure Active Directory](/azure/active-directory/) (AAD). Il utilise l’exemple d’application ASP.NET Core décrit dans [Facebook, Google et l’authentification du fournisseur externe](xref:security/authentication/social/index).

Pour les applications ASP.NET Core, la prise en charge des WS-Federation est assurée par [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). Ce composant est porté à partir de [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) et partage un grand nombre des mécanismes de ce composant. Toutefois, les composants diffèrent de deux manières importantes.

Par défaut, le nouveau Middleware :

* N’autorise pas les connexions non sollicitées. Cette fonctionnalité du protocole WS-Federation est vulnérable aux attaques XSRF. Toutefois, il peut être activé à l’aide de l' `AllowUnsolicitedLogins` option.
* Ne vérifie pas les messages de connexion dans chaque publication de formulaire. Seules les demandes à `CallbackPath` sont vérifiées pour les connexions. `CallbackPath` la valeur par défaut est, `/signin-wsfed` mais elle peut être modifiée via la propriété [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) héritée de la classe [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) . Ce chemin d’accès peut être partagé avec d’autres fournisseurs d’authentification en activant l’option [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) .

## <a name="register-the-app-with-active-directory"></a>Inscrire l’application auprès de Active Directory

### <a name="active-directory-federation-services"></a>Services de fédération Active Directory (AD FS)

* Ouvrez l' **Assistant Ajout d’approbation de partie de confiance** à partir de la console de gestion ADFS :

![Assistant Ajout d’approbation de partie de confiance : Bienvenue](ws-federation/_static/AdfsAddTrust.png)

* Choisissez d’entrer les données manuellement :

![Assistant Ajout d’approbation de partie de confiance : sélectionner une source de données](ws-federation/_static/AdfsSelectDataSource.png)

* Entrez un nom d’affichage pour la partie de confiance. Le nom n’est pas important pour l’application ASP.NET Core.

* [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) ne prend pas en charge le chiffrement de jeton. par conséquent, ne configurez pas un certificat de chiffrement de jeton :

![Assistant Ajout d’approbation de partie de confiance : configurer le certificat](ws-federation/_static/AdfsConfigureCert.png)

* Activez la prise en charge de WS-Federation protocole passif, à l’aide de l’URL de l’application. Vérifiez que le port est correct pour l’application :

![Assistant Ajout d’approbation de partie de confiance : configurer l’URL](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Il doit s’agir d’une URL HTTPs. IIS Express pouvez fournir un certificat auto-signé lors de l’hébergement de l’application pendant le développement. Kestrel requiert une configuration manuelle des certificats. Pour plus d’informations, consultez la [documentation de Kestrel](xref:fundamentals/servers/kestrel) .

* Cliquez sur **suivant** dans le reste de l’Assistant et **Fermez** à la fin.

* ASP.NET Core Identity requiert une revendication d' **ID de nom** . Ajoutez-en une à partir de la boîte de dialogue **modifier les règles de revendication** :

![Modifier les règles de revendication](ws-federation/_static/EditClaimRules.png)

* Dans l' **Assistant Ajouter une règle de revendication de transformation** , laissez le modèle par défaut **Envoyer les attributs LDAP en tant que revendications** sélectionné, puis cliquez sur **suivant** . Ajoutez une règle mappant l’attribut **Sam-Account-Name** LDAP à la revendication de **nom ID** sortante :

![Assistant Ajout de règle de revendication de transformation : configurer la règle de revendication](ws-federation/_static/AddTransformClaimRule.png)

* Cliquez sur **Terminer**  >  **OK** dans la fenêtre **modifier les règles de revendication** .

### <a name="azure-active-directory"></a>Azure Active Directory

* Accédez au panneau inscriptions des applications du locataire AAD. Cliquez sur **nouvelle inscription d’application** :

![Azure Active Directory : inscriptions d’applications](ws-federation/_static/AadNewAppRegistration.png)

* Entrez un nom pour l’inscription de l’application. Cela n’est pas important pour l’application ASP.NET Core.
* Entrez l’URL d’écoute de l’application en tant qu' **URL de connexion** :

![Azure Active Directory : créer l’inscription de l’application](ws-federation/_static/AadCreateAppRegistration.png)

* Cliquez sur **points de terminaison** et notez l’URL du document de **métadonnées de Fédération** . Il s’agit de l’intergiciel (middleware) WS-Federation `MetadataAddress` :

![Azure Active Directory : points de terminaison](ws-federation/_static/AadFederationMetadataDocument.png)

* Accédez à la nouvelle inscription d’application. Cliquez sur **exposer une API** . Cliquez sur ID de l’application URI **Set**  >  **Save** . Prenez note de l'  **URI** de l’ID de l’application. Il s’agit de l’intergiciel (middleware) WS-Federation `Wtrealm` :

![Azure Active Directory : propriétés d’inscription de l’application](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-no-locaspnet-core-identity"></a>Utilisez WS-Federation sans ASP.NET Core Identity

L’intergiciel WS-Federation peut être utilisé sans Identity . Exemple :
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-no-locaspnet-core-identity"></a>Ajouter WS-Federation en tant que fournisseur de connexion externe pour ASP.NET Core Identity

* Ajoutez une dépendance sur [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) au projet.
* Ajoutez WS-Federation à `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Connectez-vous avec WS-Federation

Accédez à l’application, puis cliquez sur le lien **se connecter** dans l’en-tête de navigation. Vous avez la possibilité de vous connecter avec WsFederation : ![ connexion à la page](ws-federation/_static/WsFederationButton.png)

Avec ADFS comme fournisseur, le bouton redirige vers une page de connexion ADFS : ![ page de connexion ADFS.](ws-federation/_static/AdfsLoginPage.png)

Avec Azure Active Directory en tant que fournisseur, le bouton redirige vers une page de connexion AAD : ![ page de connexion AAD](ws-federation/_static/AadSignIn.png)

Une connexion réussie pour un nouvel utilisateur redirige vers la page d’inscription de l’utilisateur de l’application : ![ page inscrire](ws-federation/_static/Register.png)
