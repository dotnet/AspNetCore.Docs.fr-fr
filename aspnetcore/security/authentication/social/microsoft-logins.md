---
title: Configuration de la connexion externe à un compte Microsoft avec ASP.NET Core
author: rick-anderson
description: Cet exemple illustre l’intégration de compte Microsoft l’authentification utilisateur dans une application ASP.NET Core existante.
ms.author: riande
ms.custom: mvc
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3161e4f0f735294d69dd51634b424d1ed573e615
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060298"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="52705-103">Configuration de la connexion externe à un compte Microsoft avec ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="52705-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="52705-104">Par [Valeriy Novytskyy](https://github.com/01binary) et [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="52705-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="52705-105">Cet exemple montre comment permettre aux utilisateurs de se connecter avec leur compte Microsoft professionnel, scolaire ou personnel à l’aide du projet ASP.NET Core 3,0 créé sur la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="52705-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="52705-106">Créer l’application dans le portail des développeurs Microsoft</span><span class="sxs-lookup"><span data-stu-id="52705-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="52705-107">Ajoutez le package NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) au projet.</span><span class="sxs-lookup"><span data-stu-id="52705-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="52705-108">Accédez à la page de [inscriptions d’applications portail Azure](https://go.microsoft.com/fwlink/?linkid=2083908) et créez ou connectez-vous à une compte Microsoft :</span><span class="sxs-lookup"><span data-stu-id="52705-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="52705-109">Si vous n’avez pas de compte Microsoft, sélectionnez en **créer un** .</span><span class="sxs-lookup"><span data-stu-id="52705-109">If you don't have a Microsoft account, select **Create one** .</span></span> <span data-ttu-id="52705-110">Une fois connecté, vous êtes redirigé vers la page **inscriptions d’applications** :</span><span class="sxs-lookup"><span data-stu-id="52705-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="52705-111">Sélectionner une **nouvelle inscription**</span><span class="sxs-lookup"><span data-stu-id="52705-111">Select **New registration**</span></span>
* <span data-ttu-id="52705-112">Saisissez un **Nom** .</span><span class="sxs-lookup"><span data-stu-id="52705-112">Enter a **Name** .</span></span>
* <span data-ttu-id="52705-113">Sélectionnez une option pour les **types de comptes pris en charge** .</span><span class="sxs-lookup"><span data-stu-id="52705-113">Select an option for **Supported account types** .</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * <span data-ttu-id="52705-114">Par `MicrosoftAccount` défaut, le package prend en charge les inscriptions d’application créées à l’aide de l’option « comptes dans n’importe quel annuaire d’organisation » ou « comptes dans les annuaires d’organisation et les comptes Microsoft ».</span><span class="sxs-lookup"><span data-stu-id="52705-114">The `MicrosoftAccount` package supports App Registrations created using "Accounts in any organizational directory" or "Accounts in any organizational directory and Microsoft accounts" options by default.</span></span>
  * <span data-ttu-id="52705-115">Pour utiliser d’autres options, définissez `AuthorizationEndpoint` et les `TokenEndpoint` membres de `MicrosoftAccountOptions` utilisés pour initialiser l’authentification du compte Microsoft à la page URL affichée sur les **points de terminaison** de l’inscription de l’application après sa création (disponible en cliquant sur points de terminaison dans la page **vue d’ensemble** ).</span><span class="sxs-lookup"><span data-stu-id="52705-115">To use other options, set `AuthorizationEndpoint` and `TokenEndpoint` members of `MicrosoftAccountOptions` used to initialize the Microsoft Account authentication to the URLs displayed on **Endpoints** page of the App Registration after it is created (available by clicking Endpoints on the **Overview** page).</span></span>
* <span data-ttu-id="52705-116">Sous **URI de redirection** , entrez votre URL de développement avec le `/signin-microsoft` suffixe.</span><span class="sxs-lookup"><span data-stu-id="52705-116">Under **Redirect URI** , enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="52705-117">Par exemple : `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="52705-117">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="52705-118">Le schéma d’authentification Microsoft configuré plus tard dans cet exemple gère automatiquement les demandes à `/signin-microsoft` l’itinéraire pour implémenter le Flow OAuth.</span><span class="sxs-lookup"><span data-stu-id="52705-118">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="52705-119">Sélectionnez **Inscrire** .</span><span class="sxs-lookup"><span data-stu-id="52705-119">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="52705-120">Créer un secret client</span><span class="sxs-lookup"><span data-stu-id="52705-120">Create client secret</span></span>

* <span data-ttu-id="52705-121">Dans le volet gauche, sélectionnez **Certificats et secrets** .</span><span class="sxs-lookup"><span data-stu-id="52705-121">In the left pane, select **Certificates & secrets** .</span></span>
* <span data-ttu-id="52705-122">Sous **secrets client** , sélectionnez **nouvelle clé secrète client** .</span><span class="sxs-lookup"><span data-stu-id="52705-122">Under **Client secrets** , select **New client secret**</span></span>

  * <span data-ttu-id="52705-123">Ajoutez une description pour la clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="52705-123">Add a description for the client secret.</span></span>
  * <span data-ttu-id="52705-124">Sélectionnez le bouton **Ajouter** .</span><span class="sxs-lookup"><span data-stu-id="52705-124">Select the **Add** button.</span></span>

* <span data-ttu-id="52705-125">Sous **secrets clients** , copiez la valeur de la clé secrète client.</span><span class="sxs-lookup"><span data-stu-id="52705-125">Under **Client secrets** , copy the value of the client secret.</span></span>

<span data-ttu-id="52705-126">Le segment d’URI `/signin-microsoft` est défini en tant que rappel par défaut du fournisseur d’authentification Microsoft.</span><span class="sxs-lookup"><span data-stu-id="52705-126">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="52705-127">Vous pouvez modifier l’URI de rappel par défaut lors de la configuration de l’intergiciel (middleware) d’authentification Microsoft via la propriété héritée [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) de la classe [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="52705-127">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="52705-128">Stocker l’ID client et le secret Microsoft</span><span class="sxs-lookup"><span data-stu-id="52705-128">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="52705-129">Stockez les paramètres sensibles tels que l’ID client Microsoft et les valeurs secrètes avec le [Gestionnaire de secret](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="52705-129">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="52705-130">Pour cet exemple, procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="52705-130">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="52705-131">Initialisez le projet pour le stockage secret conformément aux instructions de la procédure [activer le stockage secret](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="52705-131">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="52705-132">Stockez les paramètres sensibles dans le magasin de secret local avec les clés secrètes `Authentication:Microsoft:ClientId` et `Authentication:Microsoft:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="52705-132">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="52705-133">Configurer l’authentification de compte Microsoft</span><span class="sxs-lookup"><span data-stu-id="52705-133">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="52705-134">Ajoutez le service de compte Microsoft à `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="52705-134">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="52705-135">Pour plus d’informations sur les options de configuration prises en charge par l’authentification de compte Microsoft, consultez la référence de l’API [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="52705-135">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="52705-136">Cela peut être utilisé pour demander différentes informations sur l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="52705-136">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="52705-137">Compte Se connecter avec Microsoft</span><span class="sxs-lookup"><span data-stu-id="52705-137">Sign in with Microsoft Account</span></span>

<span data-ttu-id="52705-138">Exécutez l’application, puis cliquez sur **se connecter** .</span><span class="sxs-lookup"><span data-stu-id="52705-138">Run the app and click **Log in** .</span></span> <span data-ttu-id="52705-139">Une option permettant de se connecter à Microsoft s’affiche.</span><span class="sxs-lookup"><span data-stu-id="52705-139">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="52705-140">Lorsque vous cliquez sur Microsoft, vous êtes redirigé vers Microsoft pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="52705-140">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="52705-141">Une fois que vous êtes connecté avec votre compte Microsoft, vous êtes invité à autoriser l’application à accéder à vos informations :</span><span class="sxs-lookup"><span data-stu-id="52705-141">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="52705-142">Appuyez sur **Oui** . vous êtes alors redirigé vers le site Web où vous pouvez définir votre adresse de messagerie.</span><span class="sxs-lookup"><span data-stu-id="52705-142">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="52705-143">Vous êtes maintenant connecté à l’aide de vos informations d’identification Microsoft :</span><span class="sxs-lookup"><span data-stu-id="52705-143">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="52705-144">Dépannage</span><span class="sxs-lookup"><span data-stu-id="52705-144">Troubleshooting</span></span>

* <span data-ttu-id="52705-145">Si le fournisseur de comptes Microsoft vous redirige vers une page d’erreur de connexion, notez le titre d’erreur et la description paramètres de chaîne de requête qui suivent directement le `#` (mot-dièse) dans l’URI.</span><span class="sxs-lookup"><span data-stu-id="52705-145">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="52705-146">Bien que le message d’erreur semble indiquer un problème avec l’authentification Microsoft, la cause la plus courante est que l’URI de votre application ne correspond à aucun des **URI de redirection** spécifiés pour la plateforme **Web** .</span><span class="sxs-lookup"><span data-stu-id="52705-146">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="52705-147">Si Identity n’est pas configuré en appelant `services.AddIdentity` dans `ConfigureServices` , toute tentative d’authentification entraîne une *exception ArgumentException : l’option « SignInScheme » doit être fournie* .</span><span class="sxs-lookup"><span data-stu-id="52705-147">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="52705-148">Le modèle de projet utilisé dans cet exemple permet de s’assurer que cette opération est effectuée.</span><span class="sxs-lookup"><span data-stu-id="52705-148">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="52705-149">Si la base de données de site n’a pas été créée en appliquant la migration initiale, vous obtiendrez *une opération de base de données qui a échoué lors du traitement de l’erreur de demande* .</span><span class="sxs-lookup"><span data-stu-id="52705-149">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="52705-150">Appuyez sur **appliquer des migrations** pour créer la base de données, puis sur Actualiser pour poursuivre l’erreur.</span><span class="sxs-lookup"><span data-stu-id="52705-150">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="52705-151">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="52705-151">Next steps</span></span>

* <span data-ttu-id="52705-152">Cet article vous a montré comment vous pouvez vous authentifier auprès de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="52705-152">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="52705-153">Vous pouvez suivre une approche similaire pour vous authentifier auprès d’autres fournisseurs listés dans la [page précédente](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="52705-153">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="52705-154">Une fois que vous publiez votre site Web dans l’application Web Azure, créez un nouveau secret client dans le portail des développeurs Microsoft.</span><span class="sxs-lookup"><span data-stu-id="52705-154">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="52705-155">Définissez les `Authentication:Microsoft:ClientId` et `Authentication:Microsoft:ClientSecret` en tant que paramètres d’application dans la portail Azure.</span><span class="sxs-lookup"><span data-stu-id="52705-155">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="52705-156">Le système de configuration est configuré pour lire des clés à partir de variables d’environnement.</span><span class="sxs-lookup"><span data-stu-id="52705-156">The configuration system is set up to read keys from environment variables.</span></span>
