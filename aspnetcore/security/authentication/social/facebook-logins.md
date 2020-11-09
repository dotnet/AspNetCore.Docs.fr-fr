---
title: Configuration de la connexion externe Facebook dans ASP.NET Core
author: rick-anderson
description: Didacticiel avec des exemples de code illustrant l’intégration de l’authentification utilisateur de compte Facebook dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/facebook-logins
ms.openlocfilehash: be0b655645fd2bd0eab9f9c30a65485f386cead3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053356"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="f9130-103">Configuration de la connexion externe Facebook dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f9130-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="f9130-104">Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f9130-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="f9130-105">Ce didacticiel avec des exemples de code montre comment permettre à vos utilisateurs de se connecter avec leur compte Facebook à l’aide d’un exemple de projet ASP.NET Core 3,0 créé sur la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f9130-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="f9130-106">Nous commençons par créer un ID d’application Facebook en suivant les [étapes officielles](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="f9130-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="f9130-107">Créer l’application dans Facebook</span><span class="sxs-lookup"><span data-stu-id="f9130-107">Create the app in Facebook</span></span>

* <span data-ttu-id="f9130-108">Ajoutez le package NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) au projet.</span><span class="sxs-lookup"><span data-stu-id="f9130-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="f9130-109">Accédez à la page de l' [application développeurs Facebook](https://developers.facebook.com/apps/) et connectez-vous.</span><span class="sxs-lookup"><span data-stu-id="f9130-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="f9130-110">Si vous ne disposez pas déjà d’un compte Facebook, utilisez le lien s' **inscrire à Facebook** sur la page de connexion pour en créer un.</span><span class="sxs-lookup"><span data-stu-id="f9130-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="f9130-111">Une fois que vous disposez d’un compte Facebook, suivez les instructions pour vous inscrire en tant que développeur Facebook.</span><span class="sxs-lookup"><span data-stu-id="f9130-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="f9130-112">Dans le menu **mes applications** , sélectionnez **créer une application** pour créer un nouvel ID d’application.</span><span class="sxs-lookup"><span data-stu-id="f9130-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Portail Facebook pour développeurs ouvert dans Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="f9130-114">Remplissez le formulaire et appuyez sur le bouton **créer un ID d’application** .</span><span class="sxs-lookup"><span data-stu-id="f9130-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Créer un formulaire d’ID d’application](index/_static/FBNewAppId.png)

* <span data-ttu-id="f9130-116">Sur la nouvelle carte d’application, sélectionnez **Ajouter un produit** .</span><span class="sxs-lookup"><span data-stu-id="f9130-116">On the new App card, select **Add a Product** .</span></span>  <span data-ttu-id="f9130-117">Sur la carte de **connexion Facebook** , cliquez sur **configurer** .</span><span class="sxs-lookup"><span data-stu-id="f9130-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Page installation du produit](index/_static/FBProductSetup.png)

* <span data-ttu-id="f9130-119">L’Assistant **démarrage rapide** démarre avec **choisir une plateforme** comme première page.</span><span class="sxs-lookup"><span data-stu-id="f9130-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="f9130-120">Ignorez l’Assistant pour le moment en cliquant sur le lien **paramètres** de **connexion Facebook** dans le menu situé en bas à gauche :</span><span class="sxs-lookup"><span data-stu-id="f9130-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Ignorer Démarrage rapide](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="f9130-122">La page **paramètres OAuth du client** s’affiche :</span><span class="sxs-lookup"><span data-stu-id="f9130-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Page Paramètres OAuth du client](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="f9130-124">Entrez votre URI de développement avec */SignIn-Facebook* ajouté dans le champ **URI de redirection OAuth valide** (par exemple : `https://localhost:44320/signin-facebook` ).</span><span class="sxs-lookup"><span data-stu-id="f9130-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="f9130-125">L’authentification Facebook configurée plus tard dans ce didacticiel gère automatiquement les demandes à l’itinéraire */SignIn-Facebook* pour implémenter le Flow OAuth.</span><span class="sxs-lookup"><span data-stu-id="f9130-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="f9130-126">L’URI */SignIn-Facebook* est défini comme rappel par défaut du fournisseur d’authentification Facebook.</span><span class="sxs-lookup"><span data-stu-id="f9130-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="f9130-127">Vous pouvez modifier l’URI de rappel par défaut lors de la configuration de l’intergiciel d’authentification Facebook à l’aide de la propriété héritée [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la classe [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="f9130-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="f9130-128">Cliquez sur **Enregistrer les modifications** .</span><span class="sxs-lookup"><span data-stu-id="f9130-128">Click **Save Changes** .</span></span>

* <span data-ttu-id="f9130-129">Cliquez sur **paramètres**  >  lien de **base** dans le volet de navigation gauche.</span><span class="sxs-lookup"><span data-stu-id="f9130-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="f9130-130">Sur cette page, Notez votre `App ID` et votre `App Secret` .</span><span class="sxs-lookup"><span data-stu-id="f9130-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="f9130-131">Vous allez les ajouter à votre application ASP.NET Core dans la section suivante :</span><span class="sxs-lookup"><span data-stu-id="f9130-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="f9130-132">Lors du déploiement du site, vous devez revisiter la page de configuration de la **connexion Facebook** et inscrire un nouvel URI public.</span><span class="sxs-lookup"><span data-stu-id="f9130-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="f9130-133">Stocker l’ID et le secret de l’application Facebook</span><span class="sxs-lookup"><span data-stu-id="f9130-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="f9130-134">Stockez les paramètres sensibles tels que l’ID d’application Facebook et les valeurs secrètes avec le [Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f9130-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="f9130-135">Pour cet exemple, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="f9130-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="f9130-136">Initialisez le projet pour le stockage secret conformément aux instructions de la procédure [activer le stockage secret](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="f9130-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="f9130-137">Stockez les paramètres sensibles dans le magasin de secret local avec les clés secrètes `Authentication:Facebook:AppId` et `Authentication:Facebook:AppSecret` :</span><span class="sxs-lookup"><span data-stu-id="f9130-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="f9130-138">Configurer l’authentification Facebook</span><span class="sxs-lookup"><span data-stu-id="f9130-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="f9130-139">Ajoutez le service Facebook dans la `ConfigureServices` méthode dans le fichier *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="f9130-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="f9130-140">Se connecter avec Facebook</span><span class="sxs-lookup"><span data-stu-id="f9130-140">Sign in with Facebook</span></span>

* <span data-ttu-id="f9130-141">Exécutez l’application et sélectionnez **se connecter** .</span><span class="sxs-lookup"><span data-stu-id="f9130-141">Run the app and select **Log in** .</span></span> 
* <span data-ttu-id="f9130-142">Sous **utiliser un autre service pour se connecter** , sélectionnez Facebook.</span><span class="sxs-lookup"><span data-stu-id="f9130-142">Under **Use another service to log in.** , select Facebook.</span></span>
* <span data-ttu-id="f9130-143">Vous êtes redirigé vers **Facebook** pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="f9130-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="f9130-144">Entrez vos informations d’identification Facebook.</span><span class="sxs-lookup"><span data-stu-id="f9130-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="f9130-145">Vous êtes redirigé vers votre site où vous pouvez définir votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="f9130-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="f9130-146">Vous êtes maintenant connecté à l’aide de vos informations d’identification Facebook :</span><span class="sxs-lookup"><span data-stu-id="f9130-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="f9130-147">Réagir pour annuler autoriser la connexion externe</span><span class="sxs-lookup"><span data-stu-id="f9130-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="f9130-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> peut fournir un chemin de redirection vers l’agent utilisateur lorsque l’utilisateur n’approuve pas la demande d’autorisation demandée.</span><span class="sxs-lookup"><span data-stu-id="f9130-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="f9130-149">Le code suivant affecte `AccessDeniedPath` à la valeur `"/AccessDeniedPathInfo"` :</span><span class="sxs-lookup"><span data-stu-id="f9130-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="f9130-150">Nous vous recommandons d’utiliser la `AccessDeniedPath` page contenant les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="f9130-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="f9130-151">L’authentification distante a été annulée.</span><span class="sxs-lookup"><span data-stu-id="f9130-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="f9130-152">Cette application nécessite une authentification.</span><span class="sxs-lookup"><span data-stu-id="f9130-152">This app requires authentication.</span></span>
* <span data-ttu-id="f9130-153">Pour réessayer de vous connecter, sélectionnez le lien de connexion.</span><span class="sxs-lookup"><span data-stu-id="f9130-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="f9130-154">AccessDeniedPath de test</span><span class="sxs-lookup"><span data-stu-id="f9130-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="f9130-155">Accédez à [Facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="f9130-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="f9130-156">Si vous êtes connecté, vous devez vous déconnecter.</span><span class="sxs-lookup"><span data-stu-id="f9130-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="f9130-157">Exécutez l’application et sélectionnez connexion Facebook.</span><span class="sxs-lookup"><span data-stu-id="f9130-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="f9130-158">Sélectionnez **pas maintenant** .</span><span class="sxs-lookup"><span data-stu-id="f9130-158">Select **Not now** .</span></span> <span data-ttu-id="f9130-159">Vous êtes redirigé vers la page spécifiée `AccessDeniedPath` .</span><span class="sxs-lookup"><span data-stu-id="f9130-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="f9130-160">Pour plus d’informations sur les options de configuration prises en charge par l’authentification Facebook, consultez la référence de l’API [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="f9130-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="f9130-161">Les options de configuration peuvent être utilisées pour :</span><span class="sxs-lookup"><span data-stu-id="f9130-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="f9130-162">Demander des informations différentes sur l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="f9130-162">Request different information about the user.</span></span>
* <span data-ttu-id="f9130-163">Ajoutez des arguments de chaîne de requête pour personnaliser l’expérience de connexion.</span><span class="sxs-lookup"><span data-stu-id="f9130-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f9130-164">Dépannage</span><span class="sxs-lookup"><span data-stu-id="f9130-164">Troubleshooting</span></span>

* <span data-ttu-id="f9130-165">**ASP.net Core 2. x uniquement :** Si Identity n’est pas configuré en appelant `services.AddIdentity` dans `ConfigureServices` , toute tentative d’authentification entraîne une *exception ArgumentException : l’option « SignInScheme » doit être fournie* .</span><span class="sxs-lookup"><span data-stu-id="f9130-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="f9130-166">Le modèle de projet utilisé dans ce didacticiel permet d’effectuer cette opération.</span><span class="sxs-lookup"><span data-stu-id="f9130-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="f9130-167">Si la base de données de site n’a pas été créée en appliquant la migration initiale, vous recevez *une opération de base de données qui a échoué lors du traitement de l’erreur de demande* .</span><span class="sxs-lookup"><span data-stu-id="f9130-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="f9130-168">Appuyez sur **appliquer des migrations** pour créer la base de données, puis sur Actualiser pour poursuivre l’erreur.</span><span class="sxs-lookup"><span data-stu-id="f9130-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f9130-169">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="f9130-169">Next steps</span></span>

* <span data-ttu-id="f9130-170">Cet article vous a montré comment vous pouvez vous authentifier avec Facebook.</span><span class="sxs-lookup"><span data-stu-id="f9130-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="f9130-171">Vous pouvez suivre une approche similaire pour vous authentifier auprès d’autres fournisseurs listés dans la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="f9130-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="f9130-172">Une fois que vous publiez votre site Web sur Azure Web App, vous devez réinitialiser le `AppSecret` dans le portail des développeurs Facebook.</span><span class="sxs-lookup"><span data-stu-id="f9130-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="f9130-173">Définissez les `Authentication:Facebook:AppId` et `Authentication:Facebook:AppSecret` en tant que paramètres d’application dans la portail Azure.</span><span class="sxs-lookup"><span data-stu-id="f9130-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="f9130-174">Le système de configuration est configuré pour lire des clés à partir de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="f9130-174">The configuration system is set up to read keys from environment variables.</span></span>
