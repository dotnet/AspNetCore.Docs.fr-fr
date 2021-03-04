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
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="86321-103">Configuration de la connexion à Google External dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="86321-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="86321-104">Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="86321-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="86321-105">Ce didacticiel vous montre comment permettre aux utilisateurs de se connecter avec leur compte Google à l’aide du projet ASP.NET Core 3,0 créé sur la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="86321-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="86321-106">Créer un projet de console d’API Google et un ID client</span><span class="sxs-lookup"><span data-stu-id="86321-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="86321-107">Ajoutez le package NuGet [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) à l’application.</span><span class="sxs-lookup"><span data-stu-id="86321-107">Add the [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) NuGet package to the app.</span></span>
* <span data-ttu-id="86321-108">Suivez les instructions de la section [intégration de google Sign-In à votre application Web](https://developers.google.com/identity/sign-in/web/sign-in) (documentation Google).</span><span class="sxs-lookup"><span data-stu-id="86321-108">Follow the guidance in [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) (Google documentation).</span></span>
* <span data-ttu-id="86321-109">Dans la page **informations d’identification** de la [console Google](https://console.developers.google.com/apis/credentials), sélectionnez créer des **informations d’identification**  >  **client OAuth ID**.</span><span class="sxs-lookup"><span data-stu-id="86321-109">In the **Credentials** page of the [Google console](https://console.developers.google.com/apis/credentials), select **CREATE CREDENTIALS** > **OAuth client ID**.</span></span>
* <span data-ttu-id="86321-110">Dans la boîte de dialogue **type d’application** , sélectionnez **application Web**.</span><span class="sxs-lookup"><span data-stu-id="86321-110">In the **Application type** dialog, select **Web application**.</span></span> <span data-ttu-id="86321-111">Fournissez un **nom** pour l’application.</span><span class="sxs-lookup"><span data-stu-id="86321-111">Provide a **Name** for the app.</span></span>
* <span data-ttu-id="86321-112">Dans la section **URI de redirection autorisés** , sélectionnez **Ajouter un URI** pour définir l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="86321-112">In the **Authorized redirect URIs** section, select **ADD URI** to set the redirect URI.</span></span> <span data-ttu-id="86321-113">Exemple d’URI de redirection : `https://localhost:{PORT}/signin-google` , où l' `{PORT}` espace réservé est le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="86321-113">Example redirect URI: `https://localhost:{PORT}/signin-google`, where the `{PORT}` placeholder is the app's port.</span></span>
* <span data-ttu-id="86321-114">Sélectionnez le bouton **créer** .</span><span class="sxs-lookup"><span data-stu-id="86321-114">Select the **CREATE** button.</span></span>
* <span data-ttu-id="86321-115">Enregistrez l' **ID client** et la **clé secrète client** pour les utiliser dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="86321-115">Save the **Client ID** and **Client Secret** for use in the app's configuration.</span></span>
* <span data-ttu-id="86321-116">Lors du déploiement du site, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="86321-116">When deploying the site, either:</span></span>
  * <span data-ttu-id="86321-117">Mettez à jour l’URI de redirection de l’application dans la **console Google** vers l’URI de redirection déployée de l’application.</span><span class="sxs-lookup"><span data-stu-id="86321-117">Update the app's redirect URI in the **Google Console** to the app's deployed redirect URI.</span></span>
  * <span data-ttu-id="86321-118">Créez une nouvelle inscription d’API Google dans la **console Google** pour l’application de production avec son URI de redirection de production.</span><span class="sxs-lookup"><span data-stu-id="86321-118">Create a new Google API registration in the **Google Console** for the production app with its production redirect URI.</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="86321-119">Stocker l’ID et le secret du client Google</span><span class="sxs-lookup"><span data-stu-id="86321-119">Store the Google client ID and secret</span></span>

<span data-ttu-id="86321-120">Stockez les paramètres sensibles tels que l’ID client Google et les valeurs secrètes avec le [Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="86321-120">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="86321-121">Pour cet exemple, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="86321-121">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="86321-122">Initialisez le projet pour le stockage secret conformément aux instructions de la procédure [activer le stockage secret](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="86321-122">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="86321-123">Stockez les paramètres sensibles dans le magasin de secret local avec les clés secrètes `Authentication:Google:ClientId` et `Authentication:Google:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="86321-123">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="86321-124">Vous pouvez gérer les informations d’identification et l’utilisation de votre API dans la [console d’API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="86321-124">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="86321-125">Configurer l’authentification Google</span><span class="sxs-lookup"><span data-stu-id="86321-125">Configure Google authentication</span></span>

<span data-ttu-id="86321-126">Ajoutez le service Google à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="86321-126">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="86321-127">Se connecter avec Google</span><span class="sxs-lookup"><span data-stu-id="86321-127">Sign in with Google</span></span>

* <span data-ttu-id="86321-128">Exécutez l’application, puis cliquez sur **se connecter**.</span><span class="sxs-lookup"><span data-stu-id="86321-128">Run the app and click **Log in**.</span></span> <span data-ttu-id="86321-129">Une option de connexion avec Google s’affiche.</span><span class="sxs-lookup"><span data-stu-id="86321-129">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="86321-130">Cliquez sur le bouton **Google** , qui redirige vers Google pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="86321-130">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="86321-131">Après avoir entré vos informations d’identification Google, vous êtes redirigé vers le site Web.</span><span class="sxs-lookup"><span data-stu-id="86321-131">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="86321-132"><xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Pour plus d’informations sur les options de configuration prises en charge par l’authentification Google, consultez la référence de l’API.</span><span class="sxs-lookup"><span data-stu-id="86321-132">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="86321-133">Cela peut être utilisé pour demander différentes informations sur l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="86321-133">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="86321-134">Modifier l’URI de rappel par défaut</span><span class="sxs-lookup"><span data-stu-id="86321-134">Change the default callback URI</span></span>

<span data-ttu-id="86321-135">Le segment `/signin-google` d’URI est défini en tant que rappel par défaut du fournisseur d’authentification Google.</span><span class="sxs-lookup"><span data-stu-id="86321-135">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="86321-136">Vous pouvez modifier l’URI de rappel par défaut lors de la configuration de l’intergiciel d’authentification Google via la <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType> propriété Inherited) de la <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> classe.</span><span class="sxs-lookup"><span data-stu-id="86321-136">You can change the default callback URI while configuring the Google authentication middleware via the inherited <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CallbackPath?displayProperty=nameWithType>) property of the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="86321-137">Dépannage</span><span class="sxs-lookup"><span data-stu-id="86321-137">Troubleshooting</span></span>

* <span data-ttu-id="86321-138">Si la connexion ne fonctionne pas et que vous ne recevez pas d’erreurs, passez en mode développement pour faciliter le débogage du problème.</span><span class="sxs-lookup"><span data-stu-id="86321-138">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="86321-139">Si Identity n’est pas configuré en appelant `services.AddIdentity` dans `ConfigureServices` , toute tentative d’authentification des résultats dans *ArgumentException : l’option « SignInScheme » doit être fournie*.</span><span class="sxs-lookup"><span data-stu-id="86321-139">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="86321-140">Le modèle de projet utilisé dans ce didacticiel permet d’effectuer cette opération.</span><span class="sxs-lookup"><span data-stu-id="86321-140">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="86321-141">Si la base de données de site n’a pas été créée en appliquant la migration initiale, vous recevez *une opération de base de données qui a échoué lors du traitement de l’erreur de demande* .</span><span class="sxs-lookup"><span data-stu-id="86321-141">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="86321-142">Sélectionnez **appliquer les migrations** pour créer la base de données, puis actualisez la page pour poursuivre l’erreur.</span><span class="sxs-lookup"><span data-stu-id="86321-142">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="86321-143">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="86321-143">Next steps</span></span>

* <span data-ttu-id="86321-144">Cet article vous a montré comment vous pouvez vous authentifier auprès de Google.</span><span class="sxs-lookup"><span data-stu-id="86321-144">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="86321-145">Vous pouvez suivre une approche similaire pour vous authentifier auprès d’autres fournisseurs listés dans la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="86321-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="86321-146">Une fois que vous avez publié l’application dans Azure, réinitialisez la `ClientSecret` dans la console de l’API Google.</span><span class="sxs-lookup"><span data-stu-id="86321-146">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="86321-147">Définissez les `Authentication:Google:ClientId` et `Authentication:Google:ClientSecret` en tant que paramètres d’application dans la portail Azure.</span><span class="sxs-lookup"><span data-stu-id="86321-147">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="86321-148">Le système de configuration est configuré pour lire des clés à partir de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="86321-148">The configuration system is set up to read keys from environment variables.</span></span>
