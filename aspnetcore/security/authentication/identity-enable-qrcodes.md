---
title: Activer la génération de code QR pour les applications TOTP Authenticator dans ASP.NET Core
author: rick-anderson
description: Découvrez comment activer la génération de code QR pour les applications TOTP Authenticator qui fonctionnent avec ASP.NET Core l’authentification à deux facteurs.
ms.author: riande
ms.date: 08/14/2018
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
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: b778e7238911ec9966edf7f0f7becd113b1e197a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060831"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>Activer la génération de code QR pour les applications TOTP Authenticator dans ASP.NET Core

::: moniker range="<= aspnetcore-2.0"

Les codes QR requièrent ASP.NET Core 2,0 ou une version ultérieure.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core est fourni avec la prise en charge des applications de l’authentificateur pour l’authentification individuelle. Les applications de l’authentificateur 2FA (authentification à deux facteurs), utilisant un algorithme de mot de passe à usage unique (TOTP), sont l’approche recommandée pour 2FA. 2FA utilise TOTP est préférable à SMS 2FA. Une application d’authentificateur fournit un code de 6 à 8 chiffres que les utilisateurs doivent entrer après avoir confirmé leur nom d’utilisateur et leur mot de passe. En général, une application authentificateur est installée sur un smartphone.

Les modèles d’application Web ASP.NET Core prennent en charge les authentificateurs, mais ne fournissent pas de prise en charge pour la génération de QRCode. Les générateurs QRCode facilitent la configuration de 2FA. Ce document vous guide tout au long de l’ajout de la génération de [code QR](https://wikipedia.org/wiki/QR_code) à la page de configuration 2FA.

L’authentification à deux facteurs ne se produit pas à l’aide d’un fournisseur d’authentification externe, tel que [Google](xref:security/authentication/google-logins) ou [Facebook](xref:security/authentication/facebook-logins). Les connexions externes sont protégées par le mécanisme fourni par le fournisseur de connexion externe. Par exemple, le fournisseur d’authentification [Microsoft](xref:security/authentication/microsoft-logins) requiert une clé matérielle ou une autre approche 2FA. Si les modèles par défaut appliquent le 2FA « local », les utilisateurs doivent satisfaire deux approches 2FA, ce qui n’est pas un scénario couramment utilisé.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>Ajout de codes QR à la page de configuration 2FA

Ces instructions utilisent *qrcode.js* à partir de https://davidshimjs.github.io/qrcodejs/ référentiel.

* Téléchargez la [ bibliothèque javascriptqrcode.js](https://davidshimjs.github.io/qrcodejs/) dans le `wwwroot\lib` dossier de votre projet.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* Suivez les instructions de [l' Identity échafaudage](xref:security/authentication/scaffold-identity) pour générer */Areas/ Identity /pages/Account/Manage/EnableAuthenticator.cshtml* .
* Dans */Areas/ Identity /pages/Account/Manage/EnableAuthenticator.cshtml* , localisez la `Scripts` section à la fin du fichier :

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Dans *pages/Account/Manage/EnableAuthenticator. cshtml* ( Razor pages) ou *views/Manage/EnableAuthenticator. cshtml* (MVC), localisez la `Scripts` section à la fin du fichier :

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* Mettez à jour la `Scripts` section pour ajouter une référence à la `qrcodejs` bibliothèque que vous avez ajoutée et un appel pour générer le code QR. Il doit se présenter comme suit :

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* Supprimez le paragraphe qui vous lie à ces instructions.

Exécutez votre application et assurez-vous que vous pouvez analyser le code QR et valider le code que l’authentificateur prouve.

## <a name="change-the-site-name-in-the-qr-code"></a>Modifier le nom du site dans le code QR

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Le nom du site dans le code QR est extrait du nom du projet que vous choisissez lors de la création initiale de votre projet. Vous pouvez le modifier en recherchant la `GenerateQrCodeUri(string email, string unformattedKey)` méthode dans le *Identity /pages/Account/Manage/EnableAuthenticator.cshtml.cs/Areas/* .

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Le nom du site dans le code QR est extrait du nom du projet que vous choisissez lors de la création initiale de votre projet. Vous pouvez le modifier en recherchant la `GenerateQrCodeUri(string email, string unformattedKey)` méthode dans le fichier *pages/Account/Manage/EnableAuthenticator. cshtml. cs* ( Razor pages) ou le fichier *Controllers/ManageController. cs* (MVC).

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Le code par défaut du modèle ressemble à ce qui suit :

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

Le deuxième paramètre de l’appel à `string.Format` est le nom de votre site, pris à partir du nom de votre solution. Vous pouvez le remplacer par n’importe quelle valeur, mais il doit toujours être encodé URL.

## <a name="using-a-different-qr-code-library"></a>Utilisation d’une autre bibliothèque de code QR

Vous pouvez remplacer la bibliothèque de code QR par votre bibliothèque par défaut. Le code HTML contient un `qrCode` élément dans lequel vous pouvez placer un code QR selon le mécanisme fourni par votre bibliothèque.

L’URL correctement mise en forme pour le code QR est disponible dans le :

* `AuthenticatorUri` propriété du modèle.
* `data-url` propriété dans l' `qrCodeData` élément.

## <a name="totp-client-and-server-time-skew"></a>Décalage horaire du client et du serveur TOTP

L’authentification TOTP (temps One-Time mot de passe) dépend à la fois du serveur et du périphérique authentificateur dont l’heure est précise. Jetons uniquement en dernier pendant 30 secondes. Si les connexions TOTP 2FA échouent, vérifiez que l’heure du serveur est exacte et synchronisée de préférence avec un service NTP précis.

::: moniker-end
