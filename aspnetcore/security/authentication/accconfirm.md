---
title: Account confirmation and password recovery in ASP.NET Core (Confirmation de compte et récupération de mot de passe dans ASP.NET Core)
author: rick-anderson
description: Découvrez comment créer une application ASP.NET Core avec une confirmation par e-mail et une réinitialisation du mot de passe.
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: 91148c67d5dc0bf97e2f926f50dcff5dd0708f4b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052316"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="d1cb5-103">Account confirmation and password recovery in ASP.NET Core (Confirmation de compte et récupération de mot de passe dans ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="d1cb5-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="d1cb5-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)et [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="d1cb5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="d1cb5-105">Ce didacticiel montre comment créer une application ASP.NET Core avec une confirmation par e-mail et une réinitialisation du mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="d1cb5-106">Ce didacticiel n’est **pas** une rubrique de départ.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="d1cb5-107">Vous devez connaître les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-107">You should be familiar with:</span></span>

* [<span data-ttu-id="d1cb5-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d1cb5-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="d1cb5-109">Authentification</span><span class="sxs-lookup"><span data-stu-id="d1cb5-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="d1cb5-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d1cb5-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="d1cb5-111">Prérequis</span><span class="sxs-lookup"><span data-stu-id="d1cb5-111">Prerequisites</span></span>

[<span data-ttu-id="d1cb5-112">.NET Core 3,0 SDK ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="d1cb5-112">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="d1cb5-113">Créer et tester une application Web avec l’authentification</span><span class="sxs-lookup"><span data-stu-id="d1cb5-113">Create and test a web app with authentication</span></span>

<span data-ttu-id="d1cb5-114">Exécutez les commandes suivantes pour créer une application Web avec l’authentification.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-114">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="d1cb5-115">Exécutez l’application, sélectionnez le lien **Register** et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-115">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="d1cb5-116">Une fois inscrit, vous êtes redirigé vers la `/:::no-loc(Identity):::/Account/RegisterConfirmation` page vers qui contient un lien pour simuler la confirmation de l’e-mail :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-116">Once registered, you are redirected to the to `/:::no-loc(Identity):::/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="d1cb5-117">Sélectionnez le `Click here to confirm your account` lien.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-117">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="d1cb5-118">Sélectionnez le lien de **connexion** et connectez-vous avec les mêmes informations d’identification.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-118">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="d1cb5-119">Sélectionnez le `Hello YourEmail@provider.com!` lien, qui vous redirige vers la `/:::no-loc(Identity):::/Account/Manage/PersonalData` page.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-119">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/:::no-loc(Identity):::/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="d1cb5-120">Sélectionnez l’onglet **données personnelles** sur la gauche, puis sélectionnez **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-120">Select the **Personal data** tab on the left, and then select **Delete** .</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="d1cb5-121">Configurer un fournisseur de messagerie</span><span class="sxs-lookup"><span data-stu-id="d1cb5-121">Configure an email provider</span></span>

<span data-ttu-id="d1cb5-122">Dans ce didacticiel, [SendGrid](https://sendgrid.com) est utilisé pour envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-122">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="d1cb5-123">Vous avez besoin d’un compte et d’une clé SendGrid pour envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-123">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="d1cb5-124">Vous pouvez utiliser d’autres fournisseurs de courrier électronique.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-124">You can use other email providers.</span></span> <span data-ttu-id="d1cb5-125">Nous vous recommandons d’utiliser SendGrid ou un autre service de messagerie pour envoyer des courriers électroniques.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-125">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="d1cb5-126">SMTP est difficile à sécuriser et à configurer correctement.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-126">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="d1cb5-127">Le compte SendGrid peut nécessiter l' [Ajout d’un expéditeur](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-127">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="d1cb5-128">Créez une classe pour extraire la clé de messagerie sécurisée.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="d1cb5-129">Pour cet exemple, créez *services/AuthMessageSenderOptions. cs* :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-129">For this sample, create *Services/AuthMessageSenderOptions.cs* :</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="d1cb5-130">Configurer des secrets d’utilisateur SendGrid</span><span class="sxs-lookup"><span data-stu-id="d1cb5-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="d1cb5-131">Définissez les `SendGridUser` et `SendGridKey` avec l' [outil de gestion de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="d1cb5-132">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="d1cb5-133">Sur Windows, le gestionnaire de secret stocke les paires clé/valeur dans un *secrets.jssur* le fichier dans le `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` répertoire.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="d1cb5-134">Le contenu du *secrets.jssur* le fichier n’est pas chiffré.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="d1cb5-135">Le balisage suivant montre l' *secrets.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="d1cb5-136">La `SendGridKey` valeur a été supprimée.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="d1cb5-137">Pour plus d’informations, consultez le [modèle d’options](xref:fundamentals/configuration/options) et la [configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="d1cb5-138">Installer SendGrid</span><span class="sxs-lookup"><span data-stu-id="d1cb5-138">Install SendGrid</span></span>

<span data-ttu-id="d1cb5-139">Ce didacticiel montre comment ajouter des notifications par courrier électronique par le biais de [SendGrid](https://sendgrid.com/), mais vous pouvez envoyer des courriers électroniques à l’aide de SMTP et d’autres mécanismes.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="d1cb5-140">Installez le `SendGrid` package NuGet :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d1cb5-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1cb5-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d1cb5-142">À partir de la console du gestionnaire de package, entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="d1cb5-143">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="d1cb5-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d1cb5-144">À partir de la console, entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="d1cb5-145">Pour vous inscrire à un compte SendGrid gratuit, consultez [prise en main de SendGrid](https://sendgrid.com/free/) gratuitement.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="d1cb5-146">Implémenter IEmailSender</span><span class="sxs-lookup"><span data-stu-id="d1cb5-146">Implement IEmailSender</span></span>

<span data-ttu-id="d1cb5-147">Pour implémenter `IEmailSender` , créez *services/EMailSender. cs* avec du code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="d1cb5-148">Configurer le démarrage pour la prise en charge de la messagerie</span><span class="sxs-lookup"><span data-stu-id="d1cb5-148">Configure startup to support email</span></span>

<span data-ttu-id="d1cb5-149">Ajoutez le code suivant à la `ConfigureServices` méthode dans le fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="d1cb5-150">Ajoutez `EmailSender` en tant que service temporaire.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="d1cb5-151">Inscrivez l' `AuthMessageSenderOptions` instance de configuration.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a><span data-ttu-id="d1cb5-152">RegisterConfirmation de structure</span><span class="sxs-lookup"><span data-stu-id="d1cb5-152">Scaffold RegisterConfirmation</span></span>

<span data-ttu-id="d1cb5-153">Suivez les instructions pour [l' :::no-loc(Identity)::: échafaudage](xref:security/authentication/scaffold-identity) et l’échafaudage `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-153">Follow the instructions for [Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) and scaffold `RegisterConfirmation`.</span></span>

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="d1cb5-154">Inscrire, confirmer l’e-mail et réinitialiser le mot de passe</span><span class="sxs-lookup"><span data-stu-id="d1cb5-154">Register, confirm email, and reset password</span></span>

<span data-ttu-id="d1cb5-155">Exécutez l’application Web et testez le processus de confirmation et de récupération du mot de passe du compte.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-155">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="d1cb5-156">Exécuter l’application et inscrire un nouvel utilisateur</span><span class="sxs-lookup"><span data-stu-id="d1cb5-156">Run the app and register a new user</span></span>
* <span data-ttu-id="d1cb5-157">Vérifiez votre adresse de messagerie pour obtenir le lien de confirmation du compte.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-157">Check your email for the account confirmation link.</span></span> <span data-ttu-id="d1cb5-158">Consultez le [message de débogage](#debug) si vous n’obtenez pas l’e-mail.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-158">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="d1cb5-159">Cliquez sur le lien pour confirmer votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-159">Click the link to confirm your email.</span></span>
* <span data-ttu-id="d1cb5-160">Connectez-vous avec votre adresse de messagerie et votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-160">Sign in with your email and password.</span></span>
* <span data-ttu-id="d1cb5-161">Déconnectez-vous.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-161">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="d1cb5-162">Test de réinitialisation du mot de passe</span><span class="sxs-lookup"><span data-stu-id="d1cb5-162">Test password reset</span></span>

* <span data-ttu-id="d1cb5-163">Si vous êtes connecté, sélectionnez **déconnexion** .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-163">If you're signed in, select **Logout** .</span></span>
* <span data-ttu-id="d1cb5-164">Sélectionnez le lien **se connecter** , puis sélectionnez le lien vous **avez oublié votre mot de passe ?** .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-164">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="d1cb5-165">Entrez l’adresse de messagerie que vous avez utilisée pour inscrire le compte.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-165">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="d1cb5-166">Un e-mail contenant un lien pour réinitialiser votre mot de passe est envoyé.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-166">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="d1cb5-167">Vérifiez votre adresse de messagerie, puis cliquez sur le lien pour réinitialiser votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-167">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="d1cb5-168">Une fois que votre mot de passe a été réinitialisé avec succès, vous pouvez vous connecter avec votre adresse de messagerie et votre nouveau mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-168">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

<a name="resend"></a>

## <a name="resend-email-confirmation"></a><span data-ttu-id="d1cb5-169">Renvoyer l’e-mail de confirmation</span><span class="sxs-lookup"><span data-stu-id="d1cb5-169">Resend email confirmation</span></span>

<span data-ttu-id="d1cb5-170">Dans ASP.NET Core 5,0 et versions ultérieures, sélectionnez le lien **renvoyer un e-mail de confirmation** sur la page de **connexion** .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-170">In ASP.NET Core 5.0 and later, select the **Resend email confirmation** link on the **Login** page.</span></span>

### <a name="change-email-and-activity-timeout"></a><span data-ttu-id="d1cb5-171">Modifier le délai d’expiration de l’e-mail et de l’activité</span><span class="sxs-lookup"><span data-stu-id="d1cb5-171">Change email and activity timeout</span></span>

<span data-ttu-id="d1cb5-172">Le délai d’inactivité par défaut est de 14 jours.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-172">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="d1cb5-173">Le code suivant définit le délai d’expiration d’inactivité sur 5 jours :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-173">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupApp:::no-loc(Cookie):::.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="d1cb5-174">Modifier toutes les durées de vie des jetons de protection des données</span><span class="sxs-lookup"><span data-stu-id="d1cb5-174">Change all data protection token lifespans</span></span>

<span data-ttu-id="d1cb5-175">Le code suivant modifie le délai d’expiration de tous les jetons de protection des données à 3 heures :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-175">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="d1cb5-176">Les :::no-loc(Identity)::: jetons utilisateur intégrés (voir [AspNetCore/SRC/ :::no-loc(Identity)::: /Extensions.Core/SRC/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Extensions.Core/src/TokenOptions.cs) ) ont un [délai d’expiration d’un jour](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-176">The built in :::no-loc(Identity)::: user tokens (see [AspNetCore/src/:::no-loc(Identity):::/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="d1cb5-177">Modifier la durée de vie du jeton d’e-mail</span><span class="sxs-lookup"><span data-stu-id="d1cb5-177">Change the email token lifespan</span></span>

<span data-ttu-id="d1cb5-178">La durée de vie des jetons par défaut des [ :::no-loc(Identity)::: jetons utilisateur](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Extensions.Core/src/TokenOptions.cs) est d' [un jour](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-178">The default token lifespan of [the :::no-loc(Identity)::: user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="d1cb5-179">Cette section montre comment modifier la durée de vie des jetons de courrier électronique.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-179">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="d1cb5-180">Ajoutez un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personnalisé et <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-180">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="d1cb5-181">Ajoutez le fournisseur personnalisé au conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-181">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="d1cb5-182">Message de débogage</span><span class="sxs-lookup"><span data-stu-id="d1cb5-182">Debug email</span></span>

<span data-ttu-id="d1cb5-183">Si vous ne parvenez pas à utiliser le courrier électronique :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-183">If you can't get email working:</span></span>

* <span data-ttu-id="d1cb5-184">Définissez un point d’arrêt dans `EmailSender.Execute` pour vérifier que `SendGridClient.SendEmailAsync` est appelé.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-184">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="d1cb5-185">Créez une [application console pour envoyer du courrier électronique](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) à l’aide d’un code similaire à `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-185">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="d1cb5-186">Passez en revue la page [activité de messagerie](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-186">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="d1cb5-187">Vérifiez votre dossier de courrier indésirable.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-187">Check your spam folder.</span></span>
* <span data-ttu-id="d1cb5-188">Essayez un autre alias de messagerie sur un autre fournisseur de messagerie (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="d1cb5-188">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="d1cb5-189">Essayez d’envoyer à différents comptes de messagerie.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-189">Try sending to different email accounts.</span></span>

<span data-ttu-id="d1cb5-190">**Une meilleure pratique de sécurité** consiste à **ne pas** utiliser les secrets de production dans le test et le développement.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-190">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="d1cb5-191">Si vous publiez l’application sur Azure, définissez les secrets SendGrid en tant que paramètres d’application dans le portail d’application Web Azure.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-191">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="d1cb5-192">Le système de configuration est configuré pour lire des clés à partir de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-192">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="d1cb5-193">Combiner les comptes de connexion sociale et locale</span><span class="sxs-lookup"><span data-stu-id="d1cb5-193">Combine social and local login accounts</span></span>

<span data-ttu-id="d1cb5-194">Pour compléter cette section, vous devez d’abord activer un fournisseur d’authentification externe.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-194">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="d1cb5-195">Consultez [l’authentification Facebook, Google et fournisseur externe](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-195">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="d1cb5-196">Vous pouvez combiner des comptes locaux et sociaux en cliquant sur le lien de votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-196">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="d1cb5-197">Dans l’ordre suivant, « RickAndMSFT@gmail.com » est d’abord créé comme connexion locale ; Toutefois, vous pouvez d’abord créer le compte en tant que connexion sociale, puis ajouter une connexion locale.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-197">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Application Web : RickAndMSFT@gmail.com utilisateur authentifié](accconfirm/_static/rick.png)

<span data-ttu-id="d1cb5-199">Cliquez sur le lien **gérer** .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-199">Click on the **Manage** link.</span></span> <span data-ttu-id="d1cb5-200">Notez la valeur 0 externe (connexions sociales) associée à ce compte.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-200">Note the 0 external (social logins) associated with this account.</span></span>

![Gérer l’affichage](accconfirm/_static/manage.png)

<span data-ttu-id="d1cb5-202">Cliquez sur le lien vers un autre service de connexion et acceptez les demandes de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-202">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="d1cb5-203">Dans l’image suivante, Facebook est le fournisseur d’authentification externe :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-203">In the following image, Facebook is the external authentication provider:</span></span>

![Gérer l’affichage de vos connexions externes à l’objet Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="d1cb5-205">Les deux comptes ont été combinés.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-205">The two accounts have been combined.</span></span> <span data-ttu-id="d1cb5-206">Vous pouvez vous connecter avec l’un ou l’autre de ces comptes.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-206">You are able to sign in with either account.</span></span> <span data-ttu-id="d1cb5-207">Vous souhaiterez peut-être que vos utilisateurs ajoutent des comptes locaux au cas où le service d’authentification de la connexion sociale est défaillant ou qu’ils aient perdu l’accès à leur compte social.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-207">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="d1cb5-208">Activer la confirmation du compte une fois qu’un site a des utilisateurs</span><span class="sxs-lookup"><span data-stu-id="d1cb5-208">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="d1cb5-209">L’activation de la confirmation de compte sur un site avec des utilisateurs verrouille tous les utilisateurs existants.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-209">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="d1cb5-210">Les utilisateurs existants sont bloqués, car leurs comptes ne sont pas confirmés.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-210">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="d1cb5-211">Pour contourner le verrouillage de l’utilisateur existant, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-211">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="d1cb5-212">Mettez à jour la base de données pour marquer tous les utilisateurs existants comme étant confirmés.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-212">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="d1cb5-213">Confirmez les utilisateurs existants.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-213">Confirm existing users.</span></span> <span data-ttu-id="d1cb5-214">Par exemple, envoyez des e-mails par lots avec des liens de confirmation.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-214">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="d1cb5-215">Prérequis</span><span class="sxs-lookup"><span data-stu-id="d1cb5-215">Prerequisites</span></span>

[<span data-ttu-id="d1cb5-216">.NET Core 2,2 SDK ou version ultérieure</span><span class="sxs-lookup"><span data-stu-id="d1cb5-216">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a><span data-ttu-id="d1cb5-217">Créer une application Web et une structure :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="d1cb5-217">Create a web  app and scaffold :::no-loc(Identity):::</span></span>

<span data-ttu-id="d1cb5-218">Exécutez les commandes suivantes pour créer une application Web avec l’authentification.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-218">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> <span data-ttu-id="d1cb5-219">Si <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> est configuré dans `Startup.ConfigureServices` , la configuration de l' [ `[StringLength]` attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) peut être requise pour la `Password` propriété dans les pages de génération de modèles automatique :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-219">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="d1cb5-220">Une `InputModel` `Password` propriété se trouve dans le `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs` fichier après la génération de modèles automatique :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-220">An `InputModel` `Password` property is found in the `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs` file after scaffolding :::no-loc(Identity):::.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="d1cb5-221">Tester l’inscription d’un nouvel utilisateur</span><span class="sxs-lookup"><span data-stu-id="d1cb5-221">Test new user registration</span></span>

<span data-ttu-id="d1cb5-222">Exécutez l’application, sélectionnez le lien **Register** et inscrivez un utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-222">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="d1cb5-223">À ce stade, la seule validation sur l’e-mail est avec l' [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribut.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-223">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="d1cb5-224">Après avoir envoyé l’inscription, vous êtes connecté à l’application.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-224">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="d1cb5-225">Plus loin dans ce didacticiel, le code est mis à jour afin que les nouveaux utilisateurs ne puissent pas se connecter tant que leur adresse de messagerie n’a pas été validée.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-225">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="d1cb5-226">Notez que le champ de la table `EmailConfirmed` est `False` .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-226">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="d1cb5-227">Vous souhaiterez peut-être réutiliser cet e-mail à l’étape suivante lorsque l’application envoie un e-mail de confirmation.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-227">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="d1cb5-228">Cliquez avec le bouton droit sur la ligne et sélectionnez **supprimer** .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-228">Right-click on the row and select **Delete** .</span></span> <span data-ttu-id="d1cb5-229">La suppression de l’alias de messagerie le rend plus facile dans les étapes suivantes.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-229">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="d1cb5-230">Demander une confirmation par courrier électronique</span><span class="sxs-lookup"><span data-stu-id="d1cb5-230">Require email confirmation</span></span>

<span data-ttu-id="d1cb5-231">Il est recommandé de confirmer l’e-mail d’une nouvelle inscription d’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-231">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="d1cb5-232">La confirmation par courrier électronique vous permet de vérifier qu’ils n’empruntent pas l’identité d’une autre personne (c’est-à-dire qu’ils ne sont pas inscrits auprès de l’e-mail de quelqu’un d’autre).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-232">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="d1cb5-233">Supposons que vous disposiez d’un forum de discussion et que vous souhaitiez empêcher « yli@example.com » de s’inscrire en tant que «» nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-233">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="d1cb5-234">Sans confirmation par courrier électronique, « nolivetto@contoso.com » pourrait recevoir un courrier indésirable de votre application.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-234">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="d1cb5-235">Supposons que l’utilisateur a accidentellement été inscrit comme « ylo@example.com » et n’avait pas remarqué la mauvaise orthographe de « Yli ».</span><span class="sxs-lookup"><span data-stu-id="d1cb5-235">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="d1cb5-236">Ils ne peuvent pas utiliser la récupération de mot de passe car l’application n’a pas leur adresse de messagerie correcte.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-236">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="d1cb5-237">La confirmation par e-mail fournit une protection limitée des robots.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-237">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="d1cb5-238">La confirmation par e-mail ne fournit pas de protection contre les utilisateurs malveillants disposant de nombreux comptes de messagerie.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-238">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="d1cb5-239">En général, vous souhaitez empêcher les nouveaux utilisateurs de publier des données sur votre site Web avant qu’ils n’aient un e-mail confirmé.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-239">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="d1cb5-240">Mise à jour `Startup.ConfigureServices`  pour exiger un e-mail confirmé :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-240">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="d1cb5-241">`config.SignIn.RequireConfirmedEmail = true;` empêche les utilisateurs inscrits de se connecter jusqu’à ce que leur adresse de messagerie soit confirmée.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-241">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="d1cb5-242">Configurer le fournisseur de messagerie</span><span class="sxs-lookup"><span data-stu-id="d1cb5-242">Configure email provider</span></span>

<span data-ttu-id="d1cb5-243">Dans ce didacticiel, [SendGrid](https://sendgrid.com) est utilisé pour envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-243">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="d1cb5-244">Vous avez besoin d’un compte et d’une clé SendGrid pour envoyer des messages électroniques.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-244">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="d1cb5-245">Vous pouvez utiliser d’autres fournisseurs de courrier électronique.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-245">You can use other email providers.</span></span> <span data-ttu-id="d1cb5-246">ASP.NET Core 2. x comprend `System.Net.Mail` , qui vous permet d’envoyer des e-mails à partir de votre application.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-246">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="d1cb5-247">Nous vous recommandons d’utiliser SendGrid ou un autre service de messagerie pour envoyer des courriers électroniques.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-247">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="d1cb5-248">SMTP est difficile à sécuriser et à configurer correctement.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-248">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="d1cb5-249">Créez une classe pour extraire la clé de messagerie sécurisée.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-249">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="d1cb5-250">Pour cet exemple, créez *services/AuthMessageSenderOptions. cs* :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-250">For this sample, create *Services/AuthMessageSenderOptions.cs* :</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="d1cb5-251">Configurer des secrets d’utilisateur SendGrid</span><span class="sxs-lookup"><span data-stu-id="d1cb5-251">Configure SendGrid user secrets</span></span>

<span data-ttu-id="d1cb5-252">Définissez les `SendGridUser` et `SendGridKey` avec l' [outil de gestion de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-252">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="d1cb5-253">Exemple :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-253">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="d1cb5-254">Sur Windows, le gestionnaire de secret stocke les paires clé/valeur dans un *secrets.jssur* le fichier dans le `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` répertoire.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-254">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="d1cb5-255">Le contenu du *secrets.jssur* le fichier n’est pas chiffré.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-255">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="d1cb5-256">Le balisage suivant montre l' *secrets.jssur* le fichier.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-256">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="d1cb5-257">La `SendGridKey` valeur a été supprimée.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-257">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="d1cb5-258">Pour plus d’informations, consultez le [modèle d’options](xref:fundamentals/configuration/options) et la [configuration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-258">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="d1cb5-259">Installer SendGrid</span><span class="sxs-lookup"><span data-stu-id="d1cb5-259">Install SendGrid</span></span>

<span data-ttu-id="d1cb5-260">Ce didacticiel montre comment ajouter des notifications par courrier électronique par le biais de [SendGrid](https://sendgrid.com/), mais vous pouvez envoyer des courriers électroniques à l’aide de SMTP et d’autres mécanismes.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-260">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="d1cb5-261">Installez le `SendGrid` package NuGet :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-261">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d1cb5-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1cb5-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d1cb5-263">À partir de la console du gestionnaire de package, entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-263">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="d1cb5-264">CLI .NET Core</span><span class="sxs-lookup"><span data-stu-id="d1cb5-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d1cb5-265">À partir de la console, entrez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-265">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="d1cb5-266">Pour vous inscrire à un compte SendGrid gratuit, consultez [prise en main de SendGrid](https://sendgrid.com/free/) gratuitement.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-266">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="d1cb5-267">Implémenter IEmailSender</span><span class="sxs-lookup"><span data-stu-id="d1cb5-267">Implement IEmailSender</span></span>

<span data-ttu-id="d1cb5-268">Pour implémenter `IEmailSender` , créez *services/EMailSender. cs* avec du code similaire à ce qui suit :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-268">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="d1cb5-269">Configurer le démarrage pour la prise en charge de la messagerie</span><span class="sxs-lookup"><span data-stu-id="d1cb5-269">Configure startup to support email</span></span>

<span data-ttu-id="d1cb5-270">Ajoutez le code suivant à la `ConfigureServices` méthode dans le fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-270">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="d1cb5-271">Ajoutez `EmailSender` en tant que service temporaire.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-271">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="d1cb5-272">Inscrivez l' `AuthMessageSenderOptions` instance de configuration.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-272">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="d1cb5-273">Activer la récupération du mot de passe et la confirmation du compte</span><span class="sxs-lookup"><span data-stu-id="d1cb5-273">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="d1cb5-274">Le modèle dispose du code pour la confirmation du compte et la récupération du mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-274">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="d1cb5-275">Recherchez la `OnPostAsync` méthode dans *Areas/ :::no-loc(Identity)::: /pages/Account/Register.cshtml.cs* .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-275">Find the `OnPostAsync` method in *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* .</span></span>

<span data-ttu-id="d1cb5-276">Empêchez les utilisateurs nouvellement inscrits d’être automatiquement connectés en commentant la ligne suivante :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-276">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="d1cb5-277">La méthode Complete est affichée avec la ligne modifiée mise en surbrillance :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-277">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="d1cb5-278">Inscrire, confirmer l’e-mail et réinitialiser le mot de passe</span><span class="sxs-lookup"><span data-stu-id="d1cb5-278">Register, confirm email, and reset password</span></span>

<span data-ttu-id="d1cb5-279">Exécutez l’application Web et testez le processus de confirmation et de récupération du mot de passe du compte.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-279">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="d1cb5-280">Exécuter l’application et inscrire un nouvel utilisateur</span><span class="sxs-lookup"><span data-stu-id="d1cb5-280">Run the app and register a new user</span></span>
* <span data-ttu-id="d1cb5-281">Vérifiez votre adresse de messagerie pour obtenir le lien de confirmation du compte.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-281">Check your email for the account confirmation link.</span></span> <span data-ttu-id="d1cb5-282">Consultez le [message de débogage](#debug) si vous n’obtenez pas l’e-mail.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-282">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="d1cb5-283">Cliquez sur le lien pour confirmer votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-283">Click the link to confirm your email.</span></span>
* <span data-ttu-id="d1cb5-284">Connectez-vous avec votre adresse de messagerie et votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-284">Sign in with your email and password.</span></span>
* <span data-ttu-id="d1cb5-285">Déconnectez-vous.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-285">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="d1cb5-286">Afficher la page gérer</span><span class="sxs-lookup"><span data-stu-id="d1cb5-286">View the manage page</span></span>

<span data-ttu-id="d1cb5-287">Sélectionnez votre nom d’utilisateur dans la fenêtre navigateur : ![ navigateur avec le nom d’utilisateur](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="d1cb5-287">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="d1cb5-288">La page gérer s’affiche avec l’onglet **Profil** sélectionné.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-288">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="d1cb5-289">L' **e-mail** affiche une case à cocher indiquant que le message électronique a été confirmé.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-289">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="d1cb5-290">Test de réinitialisation du mot de passe</span><span class="sxs-lookup"><span data-stu-id="d1cb5-290">Test password reset</span></span>

* <span data-ttu-id="d1cb5-291">Si vous êtes connecté, sélectionnez **déconnexion** .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-291">If you're signed in, select **Logout** .</span></span>
* <span data-ttu-id="d1cb5-292">Sélectionnez le lien **se connecter** , puis sélectionnez le lien vous **avez oublié votre mot de passe ?** .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-292">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="d1cb5-293">Entrez l’adresse de messagerie que vous avez utilisée pour inscrire le compte.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-293">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="d1cb5-294">Un e-mail contenant un lien pour réinitialiser votre mot de passe est envoyé.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-294">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="d1cb5-295">Vérifiez votre adresse de messagerie, puis cliquez sur le lien pour réinitialiser votre mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-295">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="d1cb5-296">Une fois que votre mot de passe a été réinitialisé avec succès, vous pouvez vous connecter avec votre adresse de messagerie et votre nouveau mot de passe.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-296">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="d1cb5-297">Modifier le délai d’expiration de l’e-mail et de l’activité</span><span class="sxs-lookup"><span data-stu-id="d1cb5-297">Change email and activity timeout</span></span>

<span data-ttu-id="d1cb5-298">Le délai d’inactivité par défaut est de 14 jours.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-298">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="d1cb5-299">Le code suivant définit le délai d’expiration d’inactivité sur 5 jours :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-299">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupApp:::no-loc(Cookie):::.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="d1cb5-300">Modifier toutes les durées de vie des jetons de protection des données</span><span class="sxs-lookup"><span data-stu-id="d1cb5-300">Change all data protection token lifespans</span></span>

<span data-ttu-id="d1cb5-301">Le code suivant modifie le délai d’expiration de tous les jetons de protection des données à 3 heures :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-301">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="d1cb5-302">Les :::no-loc(Identity)::: jetons utilisateur intégrés (voir [AspNetCore/SRC/ :::no-loc(Identity)::: /Extensions.Core/SRC/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Extensions.Core/src/TokenOptions.cs) ) ont un [délai d’expiration d’un jour](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-302">The built in :::no-loc(Identity)::: user tokens (see [AspNetCore/src/:::no-loc(Identity):::/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="d1cb5-303">Modifier la durée de vie du jeton d’e-mail</span><span class="sxs-lookup"><span data-stu-id="d1cb5-303">Change the email token lifespan</span></span>

<span data-ttu-id="d1cb5-304">La durée de vie des jetons par défaut des [ :::no-loc(Identity)::: jetons utilisateur](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Extensions.Core/src/TokenOptions.cs) est d' [un jour](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-304">The default token lifespan of [the :::no-loc(Identity)::: user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/:::no-loc(Identity):::/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="d1cb5-305">Cette section montre comment modifier la durée de vie des jetons de courrier électronique.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-305">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="d1cb5-306">Ajoutez un [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) personnalisé et <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-306">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="d1cb5-307">Ajoutez le fournisseur personnalisé au conteneur de service :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-307">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="d1cb5-308">Renvoyer l’e-mail de confirmation</span><span class="sxs-lookup"><span data-stu-id="d1cb5-308">Resend email confirmation</span></span>

<span data-ttu-id="d1cb5-309">Consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-309">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="d1cb5-310">Message de débogage</span><span class="sxs-lookup"><span data-stu-id="d1cb5-310">Debug email</span></span>

<span data-ttu-id="d1cb5-311">Si vous ne parvenez pas à utiliser le courrier électronique :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-311">If you can't get email working:</span></span>

* <span data-ttu-id="d1cb5-312">Définissez un point d’arrêt dans `EmailSender.Execute` pour vérifier que `SendGridClient.SendEmailAsync` est appelé.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-312">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="d1cb5-313">Créez une [application console pour envoyer du courrier électronique](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) à l’aide d’un code similaire à `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-313">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="d1cb5-314">Passez en revue la page [activité de messagerie](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-314">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="d1cb5-315">Vérifiez votre dossier de courrier indésirable.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-315">Check your spam folder.</span></span>
* <span data-ttu-id="d1cb5-316">Essayez un autre alias de messagerie sur un autre fournisseur de messagerie (Microsoft, Yahoo, Gmail, etc.)</span><span class="sxs-lookup"><span data-stu-id="d1cb5-316">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="d1cb5-317">Essayez d’envoyer à différents comptes de messagerie.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-317">Try sending to different email accounts.</span></span>

<span data-ttu-id="d1cb5-318">**Une meilleure pratique de sécurité** consiste à **ne pas** utiliser les secrets de production dans le test et le développement.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-318">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="d1cb5-319">Si vous publiez l’application sur Azure, vous pouvez définir les secrets SendGrid en tant que paramètres d’application dans le portail d’application Web Azure.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-319">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="d1cb5-320">Le système de configuration est configuré pour lire des clés à partir de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-320">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="d1cb5-321">Combiner les comptes de connexion sociale et locale</span><span class="sxs-lookup"><span data-stu-id="d1cb5-321">Combine social and local login accounts</span></span>

<span data-ttu-id="d1cb5-322">Pour compléter cette section, vous devez d’abord activer un fournisseur d’authentification externe.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-322">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="d1cb5-323">Consultez [l’authentification Facebook, Google et fournisseur externe](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="d1cb5-323">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="d1cb5-324">Vous pouvez combiner des comptes locaux et sociaux en cliquant sur le lien de votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-324">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="d1cb5-325">Dans l’ordre suivant, « RickAndMSFT@gmail.com » est d’abord créé comme connexion locale ; Toutefois, vous pouvez d’abord créer le compte en tant que connexion sociale, puis ajouter une connexion locale.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-325">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Application Web : RickAndMSFT@gmail.com utilisateur authentifié](accconfirm/_static/rick.png)

<span data-ttu-id="d1cb5-327">Cliquez sur le lien **gérer** .</span><span class="sxs-lookup"><span data-stu-id="d1cb5-327">Click on the **Manage** link.</span></span> <span data-ttu-id="d1cb5-328">Notez la valeur 0 externe (connexions sociales) associée à ce compte.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-328">Note the 0 external (social logins) associated with this account.</span></span>

![Gérer l’affichage](accconfirm/_static/manage.png)

<span data-ttu-id="d1cb5-330">Cliquez sur le lien vers un autre service de connexion et acceptez les demandes de l’application.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-330">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="d1cb5-331">Dans l’image suivante, Facebook est le fournisseur d’authentification externe :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-331">In the following image, Facebook is the external authentication provider:</span></span>

![Gérer l’affichage de vos connexions externes à l’objet Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="d1cb5-333">Les deux comptes ont été combinés.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-333">The two accounts have been combined.</span></span> <span data-ttu-id="d1cb5-334">Vous pouvez vous connecter avec l’un ou l’autre de ces comptes.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-334">You are able to sign in with either account.</span></span> <span data-ttu-id="d1cb5-335">Vous souhaiterez peut-être que vos utilisateurs ajoutent des comptes locaux au cas où le service d’authentification de la connexion sociale est défaillant ou qu’ils aient perdu l’accès à leur compte social.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-335">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="d1cb5-336">Activer la confirmation du compte une fois qu’un site a des utilisateurs</span><span class="sxs-lookup"><span data-stu-id="d1cb5-336">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="d1cb5-337">L’activation de la confirmation de compte sur un site avec des utilisateurs verrouille tous les utilisateurs existants.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-337">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="d1cb5-338">Les utilisateurs existants sont bloqués, car leurs comptes ne sont pas confirmés.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-338">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="d1cb5-339">Pour contourner le verrouillage de l’utilisateur existant, utilisez l’une des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="d1cb5-339">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="d1cb5-340">Mettez à jour la base de données pour marquer tous les utilisateurs existants comme étant confirmés.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-340">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="d1cb5-341">Confirmez les utilisateurs existants.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-341">Confirm existing users.</span></span> <span data-ttu-id="d1cb5-342">Par exemple, envoyez des e-mails par lots avec des liens de confirmation.</span><span class="sxs-lookup"><span data-stu-id="d1cb5-342">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
