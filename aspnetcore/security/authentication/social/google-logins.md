---
title: Configuration de la connexion à Google External dans ASP.NET Core
author: rick-anderson
description: Ce didacticiel illustre l’intégration de l’authentification utilisateur de compte Google dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
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
uid: security/authentication/google-logins
ms.openlocfilehash: 111ea7c972778dfd5296d0401c16563aeaa36a63
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060311"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="8926e-103">Configuration de la connexion à Google External dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8926e-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="8926e-104">Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8926e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8926e-105">Ce didacticiel vous montre comment permettre aux utilisateurs de se connecter avec leur compte Google à l’aide du projet ASP.NET Core 3,0 créé sur la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8926e-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="8926e-106">Créer un projet de console d’API Google et un ID client</span><span class="sxs-lookup"><span data-stu-id="8926e-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="8926e-107">Installez [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span><span class="sxs-lookup"><span data-stu-id="8926e-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="8926e-108">Accédez à [intégration de Google Sign-In à votre application Web](https://developers.google.com/identity/sign-in/web/sign-in) , puis sélectionnez **configurer un projet** .</span><span class="sxs-lookup"><span data-stu-id="8926e-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) and select **Configure a project** .</span></span>
* <span data-ttu-id="8926e-109">Dans la boîte de dialogue **configurer votre client OAuth** , sélectionnez **serveur Web** .</span><span class="sxs-lookup"><span data-stu-id="8926e-109">In the **Configure your OAuth client** dialog, select **Web server** .</span></span>
* <span data-ttu-id="8926e-110">Dans la zone d’entrée de texte **URI de redirection autorisés** , définissez l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="8926e-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="8926e-111">Par exemple : `https://localhost:44312/signin-google`</span><span class="sxs-lookup"><span data-stu-id="8926e-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="8926e-112">Enregistrez l' **ID client** et la **clé secrète client** .</span><span class="sxs-lookup"><span data-stu-id="8926e-112">Save the **Client ID** and **Client Secret** .</span></span>
* <span data-ttu-id="8926e-113">Lors du déploiement du site, inscrivez la nouvelle URL publique à partir de la **console Google** .</span><span class="sxs-lookup"><span data-stu-id="8926e-113">When deploying the site, register the new public url from the **Google Console** .</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="8926e-114">Stocker l’ID et le secret du client Google</span><span class="sxs-lookup"><span data-stu-id="8926e-114">Store the Google client ID and secret</span></span>

<span data-ttu-id="8926e-115">Stockez les paramètres sensibles tels que l’ID client Google et les valeurs secrètes avec le [Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8926e-115">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8926e-116">Pour cet exemple, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="8926e-116">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="8926e-117">Initialisez le projet pour le stockage secret conformément aux instructions de la procédure [activer le stockage secret](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="8926e-117">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="8926e-118">Stockez les paramètres sensibles dans le magasin de secret local avec les clés secrètes `Authentication:Google:ClientId` et `Authentication:Google:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="8926e-118">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8926e-119">Vous pouvez gérer les informations d’identification et l’utilisation de votre API dans la [console d’API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="8926e-119">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="8926e-120">Configurer l’authentification Google</span><span class="sxs-lookup"><span data-stu-id="8926e-120">Configure Google authentication</span></span>

<span data-ttu-id="8926e-121">Ajoutez le service Google à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8926e-121">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="8926e-122">Se connecter avec Google</span><span class="sxs-lookup"><span data-stu-id="8926e-122">Sign in with Google</span></span>

* <span data-ttu-id="8926e-123">Exécutez l’application, puis cliquez sur **se connecter** .</span><span class="sxs-lookup"><span data-stu-id="8926e-123">Run the app and click **Log in** .</span></span> <span data-ttu-id="8926e-124">Une option de connexion avec Google s’affiche.</span><span class="sxs-lookup"><span data-stu-id="8926e-124">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="8926e-125">Cliquez sur le bouton **Google** , qui redirige vers Google pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="8926e-125">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="8926e-126">Après avoir entré vos informations d’identification Google, vous êtes redirigé vers le site Web.</span><span class="sxs-lookup"><span data-stu-id="8926e-126">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="8926e-127"><xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Pour plus d’informations sur les options de configuration prises en charge par l’authentification Google, consultez la référence de l’API.</span><span class="sxs-lookup"><span data-stu-id="8926e-127">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="8926e-128">Cela peut être utilisé pour demander différentes informations sur l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="8926e-128">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="8926e-129">Modifier l’URI de rappel par défaut</span><span class="sxs-lookup"><span data-stu-id="8926e-129">Change the default callback URI</span></span>

<span data-ttu-id="8926e-130">Le segment `/signin-google` d’URI est défini en tant que rappel par défaut du fournisseur d’authentification Google.</span><span class="sxs-lookup"><span data-stu-id="8926e-130">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="8926e-131">Vous pouvez modifier l’URI de rappel par défaut lors de la configuration de l’intergiciel d’authentification Google via la propriété héritée [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la classe [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .</span><span class="sxs-lookup"><span data-stu-id="8926e-131">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8926e-132">Dépannage</span><span class="sxs-lookup"><span data-stu-id="8926e-132">Troubleshooting</span></span>

* <span data-ttu-id="8926e-133">Si la connexion ne fonctionne pas et que vous ne recevez pas d’erreurs, passez en mode développement pour faciliter le débogage du problème.</span><span class="sxs-lookup"><span data-stu-id="8926e-133">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="8926e-134">Si :::no-loc(Identity)::: n’est pas configuré en appelant `services.Add:::no-loc(Identity):::` dans `ConfigureServices` , toute tentative d’authentification des résultats dans *ArgumentException : l’option « SignInScheme » doit être fournie* .</span><span class="sxs-lookup"><span data-stu-id="8926e-134">If :::no-loc(Identity)::: isn't configured by calling `services.Add:::no-loc(Identity):::` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="8926e-135">Le modèle de projet utilisé dans ce didacticiel permet d’effectuer cette opération.</span><span class="sxs-lookup"><span data-stu-id="8926e-135">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="8926e-136">Si la base de données de site n’a pas été créée en appliquant la migration initiale, vous recevez *une opération de base de données qui a échoué lors du traitement de l’erreur de demande* .</span><span class="sxs-lookup"><span data-stu-id="8926e-136">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="8926e-137">Sélectionnez **appliquer les migrations** pour créer la base de données, puis actualisez la page pour poursuivre l’erreur.</span><span class="sxs-lookup"><span data-stu-id="8926e-137">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8926e-138">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="8926e-138">Next steps</span></span>

* <span data-ttu-id="8926e-139">Cet article vous a montré comment vous pouvez vous authentifier auprès de Google.</span><span class="sxs-lookup"><span data-stu-id="8926e-139">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="8926e-140">Vous pouvez suivre une approche similaire pour vous authentifier auprès d’autres fournisseurs listés dans la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8926e-140">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="8926e-141">Une fois que vous avez publié l’application dans Azure, réinitialisez la `ClientSecret` dans la console de l’API Google.</span><span class="sxs-lookup"><span data-stu-id="8926e-141">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="8926e-142">Définissez les `Authentication:Google:ClientId` et `Authentication:Google:ClientSecret` en tant que paramètres d’application dans la portail Azure.</span><span class="sxs-lookup"><span data-stu-id="8926e-142">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="8926e-143">Le système de configuration est configuré pour lire des clés à partir de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="8926e-143">The configuration system is set up to read keys from environment variables.</span></span>
