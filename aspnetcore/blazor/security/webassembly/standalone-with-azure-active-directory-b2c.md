---
title: Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory B2C
author: guardrex
description: Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2020
no-loc:
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 76486c7170849bb184ec8573e747df095b631632
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900924"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="efd68-103">Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="efd68-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="efd68-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="efd68-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="efd68-105">Pour créer une [ Blazor WebAssembly application autonome](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification :</span><span class="sxs-lookup"><span data-stu-id="efd68-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="efd68-106">Suivez les instructions des rubriques suivantes pour créer un locataire et inscrire une application Web dans le portail Azure :</span><span class="sxs-lookup"><span data-stu-id="efd68-106">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="efd68-107">Créer un locataire AAD B2C</span><span class="sxs-lookup"><span data-stu-id="efd68-107">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="efd68-108">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="efd68-108">Record the following information:</span></span>

* <span data-ttu-id="efd68-109">AAD B2C instance (par exemple, `https://contoso.b2clogin.com/` , qui comprend la barre oblique finale) : l’instance est le schéma et l’hôte d’une inscription d’application Azure B2C, que vous pouvez trouver en ouvrant la fenêtre **points de terminaison** à partir de la page **inscriptions d’applications** de la portail Azure.</span><span class="sxs-lookup"><span data-stu-id="efd68-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="efd68-110">AAD B2C domaine principal/éditeur/locataire (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.</span><span class="sxs-lookup"><span data-stu-id="efd68-110">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="efd68-111">Suivez les instructions du [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) à nouveau pour inscrire une application AAD pour l' *`Client`* application, puis procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="efd68-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="efd68-112">Dans **Azure Active Directory** > **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="efd68-112">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="efd68-113">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor AAD B2C autonome\*\*).</span><span class="sxs-lookup"><span data-stu-id="efd68-113">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="efd68-114">Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="efd68-114">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="efd68-115">Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="efd68-115">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="efd68-116">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="efd68-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="efd68-117">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="efd68-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="efd68-118">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="efd68-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="efd68-119">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="efd68-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="efd68-120">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="efd68-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="efd68-121">Vérifiez que **Permissions** > **les autorisations accordent le consentement de l’administrateur à OpenID et offline_access autorisations** sont sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="efd68-121">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="efd68-122">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="efd68-122">Select **Register**.</span></span>

<span data-ttu-id="efd68-123">Enregistrez l’ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="efd68-123">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="efd68-124">Dans configurations de plateforme **d’authentification** , > **Platform configurations** > **application à page unique (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="efd68-124">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="efd68-125">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="efd68-125">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="efd68-126">Pour **octroi implicite**, assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="efd68-126">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="efd68-127">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="efd68-127">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="efd68-128">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="efd68-128">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="efd68-129">Dans **Azure Active Directory** > **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="efd68-129">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="efd68-130">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor AAD B2C autonome\*\*).</span><span class="sxs-lookup"><span data-stu-id="efd68-130">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="efd68-131">Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="efd68-131">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="efd68-132">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="efd68-132">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="efd68-133">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="efd68-133">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="efd68-134">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="efd68-134">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="efd68-135">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="efd68-135">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="efd68-136">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="efd68-136">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="efd68-137">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="efd68-137">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="efd68-138">Vérifiez que **Permissions** > **les autorisations accordent le consentement de l’administrateur à OpenID et offline_access autorisations** sont sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="efd68-138">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="efd68-139">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="efd68-139">Select **Register**.</span></span>

<span data-ttu-id="efd68-140">Enregistrez l’ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="efd68-140">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="efd68-141">Dans **Authentication** le > **Platform configurations** > **site Web**configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="efd68-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="efd68-142">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="efd68-142">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="efd68-143">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="efd68-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="efd68-144">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="efd68-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="efd68-145">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="efd68-145">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="efd68-146">Dans la **page**d'  >  **Azure ad B2C**des  >  **flux utilisateur**:</span><span class="sxs-lookup"><span data-stu-id="efd68-146">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="efd68-147">Créer un flux d’utilisateur d’inscription et de connexion</span><span class="sxs-lookup"><span data-stu-id="efd68-147">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="efd68-148">Au minimum, sélectionnez l' **Application claims**  >  attribut utilisateur**nom complet** des revendications d’application pour remplir le `context.User.Identity.Name` dans le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="efd68-148">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="efd68-149">Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="efd68-149">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="efd68-150">Dans un dossier vide, remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="efd68-150">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="efd68-151">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="efd68-151">Placeholder</span></span>                   | <span data-ttu-id="efd68-152">Nom du portail Azure</span><span class="sxs-lookup"><span data-stu-id="efd68-152">Azure portal name</span></span>               | <span data-ttu-id="efd68-153">Exemple</span><span class="sxs-lookup"><span data-stu-id="efd68-153">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="efd68-154">Instance</span><span class="sxs-lookup"><span data-stu-id="efd68-154">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="efd68-155">ID d’application (client)</span><span class="sxs-lookup"><span data-stu-id="efd68-155">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="efd68-156">Workflow d’inscription/de connexion</span><span class="sxs-lookup"><span data-stu-id="efd68-156">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="efd68-157">Domaine principal/serveur de publication/locataire</span><span class="sxs-lookup"><span data-stu-id="efd68-157">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="efd68-158">L’emplacement de sortie spécifié avec l' `-o|--output` option crée un dossier de projet s’il n’existe pas et fait partie du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="efd68-158">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="efd68-159">Dans le Portail Azure, l' **URI de redirection** de la configuration de plateforme de l’application est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="efd68-159">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="efd68-160">Si l’application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="efd68-160">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="efd68-161">Si le port n’a pas été configuré précédemment avec le port connu de l’application, revenez à l’inscription de l’application dans la Portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="efd68-161">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="efd68-162">Après avoir créé l’application, vous devez être en mesure d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="efd68-162">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="efd68-163">Connectez-vous à l’application à l’aide d’un compte d’utilisateur AAD.</span><span class="sxs-lookup"><span data-stu-id="efd68-163">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="efd68-164">Demander des jetons d’accès pour les API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="efd68-164">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="efd68-165">Pour plus d'informations, voir :</span><span class="sxs-lookup"><span data-stu-id="efd68-165">For more information, see:</span></span>
  * [<span data-ttu-id="efd68-166">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="efd68-166">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="efd68-167">[Démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="efd68-167">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="efd68-168">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="efd68-168">Authentication package</span></span>

<span data-ttu-id="efd68-169">Quand une application est créée pour utiliser un compte B2C individuel ( `IndividualB2C` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="efd68-169">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="efd68-170">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="efd68-170">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="efd68-171">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="efd68-171">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="efd68-172">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="efd68-172">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="efd68-173">Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.</span><span class="sxs-lookup"><span data-stu-id="efd68-173">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="efd68-174">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="efd68-174">Authentication service support</span></span>

<span data-ttu-id="efd68-175">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package.</span><span class="sxs-lookup"><span data-stu-id="efd68-175">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="efd68-176">Cette méthode configure tous les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="efd68-176">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="efd68-177">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="efd68-177">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="efd68-178">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="efd68-178">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="efd68-179">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="efd68-179">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="efd68-180">La configuration est fournie par le `wwwroot/appsettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="efd68-180">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="efd68-181">Exemple :</span><span class="sxs-lookup"><span data-stu-id="efd68-181">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="efd68-182">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="efd68-182">Access token scopes</span></span>

<span data-ttu-id="efd68-183">Le Blazor WebAssembly modèle ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="efd68-183">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="efd68-184">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par défaut du <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="efd68-184">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="efd68-185">Spécifier des étendues supplémentaires avec `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="efd68-185">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="efd68-186">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="efd68-186">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="efd68-187">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="efd68-187">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="efd68-188">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="efd68-188">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="efd68-189">Mode de connexion</span><span class="sxs-lookup"><span data-stu-id="efd68-189">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="efd68-190">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="efd68-190">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="efd68-191">Page d'index</span><span class="sxs-lookup"><span data-stu-id="efd68-191">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="efd68-192">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="efd68-192">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="efd68-193">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="efd68-193">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="efd68-194">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="efd68-194">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="efd68-195">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="efd68-195">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="efd68-196">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="efd68-196">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="efd68-197">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="efd68-197">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="efd68-198">Tutoriel : Créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="efd68-198">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="efd68-199">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="efd68-199">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
