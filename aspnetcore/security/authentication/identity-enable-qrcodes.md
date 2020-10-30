---
title: Activer la génération de code QR pour les applications TOTP Authenticator dans ASP.NET Core
author: rick-anderson
description: Découvrez comment activer la génération de code QR pour les applications TOTP Authenticator qui fonctionnent avec ASP.NET Core l’authentification à deux facteurs.
ms.author: riande
ms.date: 08/14/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: b778e7238911ec9966edf7f0f7becd113b1e197a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060831"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="bd195-103">Activer la génération de code QR pour les applications TOTP Authenticator dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bd195-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="bd195-104">Les codes QR requièrent ASP.NET Core 2,0 ou une version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="bd195-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="bd195-105">ASP.NET Core est fourni avec la prise en charge des applications de l’authentificateur pour l’authentification individuelle.</span><span class="sxs-lookup"><span data-stu-id="bd195-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="bd195-106">Les applications de l’authentificateur 2FA (authentification à deux facteurs), utilisant un algorithme de mot de passe à usage unique (TOTP), sont l’approche recommandée pour 2FA.</span><span class="sxs-lookup"><span data-stu-id="bd195-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="bd195-107">2FA utilise TOTP est préférable à SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="bd195-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="bd195-108">Une application d’authentificateur fournit un code de 6 à 8 chiffres que les utilisateurs doivent entrer après avoir confirmé leur nom d’utilisateur et leur mot de passe.</span><span class="sxs-lookup"><span data-stu-id="bd195-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="bd195-109">En général, une application authentificateur est installée sur un smartphone.</span><span class="sxs-lookup"><span data-stu-id="bd195-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="bd195-110">Les modèles d’application Web ASP.NET Core prennent en charge les authentificateurs, mais ne fournissent pas de prise en charge pour la génération de QRCode.</span><span class="sxs-lookup"><span data-stu-id="bd195-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="bd195-111">Les générateurs QRCode facilitent la configuration de 2FA.</span><span class="sxs-lookup"><span data-stu-id="bd195-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="bd195-112">Ce document vous guide tout au long de l’ajout de la génération de [code QR](https://wikipedia.org/wiki/QR_code) à la page de configuration 2FA.</span><span class="sxs-lookup"><span data-stu-id="bd195-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="bd195-113">L’authentification à deux facteurs ne se produit pas à l’aide d’un fournisseur d’authentification externe, tel que [Google](xref:security/authentication/google-logins) ou [Facebook](xref:security/authentication/facebook-logins).</span><span class="sxs-lookup"><span data-stu-id="bd195-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="bd195-114">Les connexions externes sont protégées par le mécanisme fourni par le fournisseur de connexion externe.</span><span class="sxs-lookup"><span data-stu-id="bd195-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="bd195-115">Par exemple, le fournisseur d’authentification [Microsoft](xref:security/authentication/microsoft-logins) requiert une clé matérielle ou une autre approche 2FA.</span><span class="sxs-lookup"><span data-stu-id="bd195-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="bd195-116">Si les modèles par défaut appliquent le 2FA « local », les utilisateurs doivent satisfaire deux approches 2FA, ce qui n’est pas un scénario couramment utilisé.</span><span class="sxs-lookup"><span data-stu-id="bd195-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="bd195-117">Ajout de codes QR à la page de configuration 2FA</span><span class="sxs-lookup"><span data-stu-id="bd195-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="bd195-118">Ces instructions utilisent *qrcode.js* à partir de https://davidshimjs.github.io/qrcodejs/ référentiel.</span><span class="sxs-lookup"><span data-stu-id="bd195-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="bd195-119">Téléchargez la [ bibliothèque javascriptqrcode.js](https://davidshimjs.github.io/qrcodejs/) dans le `wwwroot\lib` dossier de votre projet.</span><span class="sxs-lookup"><span data-stu-id="bd195-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="bd195-120">Suivez les instructions de [l' :::no-loc(Identity)::: échafaudage](xref:security/authentication/scaffold-identity) pour générer */Areas/ :::no-loc(Identity)::: /pages/Account/Manage/EnableAuthenticator.cshtml* .</span><span class="sxs-lookup"><span data-stu-id="bd195-120">Follow the instructions in [Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) to generate */Areas/:::no-loc(Identity):::/Pages/Account/Manage/EnableAuthenticator.cshtml* .</span></span>
* <span data-ttu-id="bd195-121">Dans */Areas/ :::no-loc(Identity)::: /pages/Account/Manage/EnableAuthenticator.cshtml* , localisez la `Scripts` section à la fin du fichier :</span><span class="sxs-lookup"><span data-stu-id="bd195-121">In */Areas/:::no-loc(Identity):::/Pages/Account/Manage/EnableAuthenticator.cshtml* , locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="bd195-122">Dans *pages/Account/Manage/EnableAuthenticator. cshtml* ( :::no-loc(Razor)::: pages) ou *views/Manage/EnableAuthenticator. cshtml* (MVC), localisez la `Scripts` section à la fin du fichier :</span><span class="sxs-lookup"><span data-stu-id="bd195-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (:::no-loc(Razor)::: Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="bd195-123">Mettez à jour la `Scripts` section pour ajouter une référence à la `qrcodejs` bibliothèque que vous avez ajoutée et un appel pour générer le code QR.</span><span class="sxs-lookup"><span data-stu-id="bd195-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="bd195-124">Il doit se présenter comme suit :</span><span class="sxs-lookup"><span data-stu-id="bd195-124">It should look as follows:</span></span>

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

* <span data-ttu-id="bd195-125">Supprimez le paragraphe qui vous lie à ces instructions.</span><span class="sxs-lookup"><span data-stu-id="bd195-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="bd195-126">Exécutez votre application et assurez-vous que vous pouvez analyser le code QR et valider le code que l’authentificateur prouve.</span><span class="sxs-lookup"><span data-stu-id="bd195-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="bd195-127">Modifier le nom du site dans le code QR</span><span class="sxs-lookup"><span data-stu-id="bd195-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="bd195-128">Le nom du site dans le code QR est extrait du nom du projet que vous choisissez lors de la création initiale de votre projet.</span><span class="sxs-lookup"><span data-stu-id="bd195-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="bd195-129">Vous pouvez le modifier en recherchant la `GenerateQrCodeUri(string email, string unformattedKey)` méthode dans le *:::no-loc(Identity)::: /pages/Account/Manage/EnableAuthenticator.cshtml.cs/Areas/* .</span><span class="sxs-lookup"><span data-stu-id="bd195-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/:::no-loc(Identity):::/Pages/Account/Manage/EnableAuthenticator.cshtml.cs* .</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="bd195-130">Le nom du site dans le code QR est extrait du nom du projet que vous choisissez lors de la création initiale de votre projet.</span><span class="sxs-lookup"><span data-stu-id="bd195-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="bd195-131">Vous pouvez le modifier en recherchant la `GenerateQrCodeUri(string email, string unformattedKey)` méthode dans le fichier *pages/Account/Manage/EnableAuthenticator. cshtml. cs* ( :::no-loc(Razor)::: pages) ou le fichier *Controllers/ManageController. cs* (MVC).</span><span class="sxs-lookup"><span data-stu-id="bd195-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (:::no-loc(Razor)::: Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="bd195-132">Le code par défaut du modèle ressemble à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="bd195-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode(":::no-loc(Razor)::: Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="bd195-133">Le deuxième paramètre de l’appel à `string.Format` est le nom de votre site, pris à partir du nom de votre solution.</span><span class="sxs-lookup"><span data-stu-id="bd195-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="bd195-134">Vous pouvez le remplacer par n’importe quelle valeur, mais il doit toujours être encodé URL.</span><span class="sxs-lookup"><span data-stu-id="bd195-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="bd195-135">Utilisation d’une autre bibliothèque de code QR</span><span class="sxs-lookup"><span data-stu-id="bd195-135">Using a different QR Code library</span></span>

<span data-ttu-id="bd195-136">Vous pouvez remplacer la bibliothèque de code QR par votre bibliothèque par défaut.</span><span class="sxs-lookup"><span data-stu-id="bd195-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="bd195-137">Le code HTML contient un `qrCode` élément dans lequel vous pouvez placer un code QR selon le mécanisme fourni par votre bibliothèque.</span><span class="sxs-lookup"><span data-stu-id="bd195-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="bd195-138">L’URL correctement mise en forme pour le code QR est disponible dans le :</span><span class="sxs-lookup"><span data-stu-id="bd195-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="bd195-139">`AuthenticatorUri` propriété du modèle.</span><span class="sxs-lookup"><span data-stu-id="bd195-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="bd195-140">`data-url` propriété dans l' `qrCodeData` élément.</span><span class="sxs-lookup"><span data-stu-id="bd195-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="bd195-141">Décalage horaire du client et du serveur TOTP</span><span class="sxs-lookup"><span data-stu-id="bd195-141">TOTP client and server time skew</span></span>

<span data-ttu-id="bd195-142">L’authentification TOTP (temps One-Time mot de passe) dépend à la fois du serveur et du périphérique authentificateur dont l’heure est précise.</span><span class="sxs-lookup"><span data-stu-id="bd195-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="bd195-143">Jetons uniquement en dernier pendant 30 secondes.</span><span class="sxs-lookup"><span data-stu-id="bd195-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="bd195-144">Si les connexions TOTP 2FA échouent, vérifiez que l’heure du serveur est exacte et synchronisée de préférence avec un service NTP précis.</span><span class="sxs-lookup"><span data-stu-id="bd195-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
