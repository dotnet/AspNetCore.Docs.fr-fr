---
title: 'Sécuriser une :::no-loc(Blazor WebAssembly)::: application ASP.net Core autonome avec Azure Active Directory B2C'
author: guardrex
description: 'Découvrez comment sécuriser une :::no-loc(Blazor WebAssembly)::: application ASP.net Core autonome avec Azure Active Directory B2C.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 14eda03419e22538e17b7b4d6fa697d61cb384c8
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343703"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="190b4-103">Sécuriser une :::no-loc(Blazor WebAssembly)::: application ASP.net Core autonome avec Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="190b4-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="190b4-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="190b4-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="190b4-105">Cet article explique comment créer une [ :::no-loc(Blazor WebAssembly)::: application autonome](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="190b4-105">This article covers how to create a [standalone :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

<span data-ttu-id="190b4-106">Créez un locataire ou identifiez un locataire B2C existant que l’application doit utiliser dans la Portail Azure en suivant les instructions de l’article [créer un locataire AAD B2C (documentation Azure)](/azure/active-directory-b2c/tutorial-create-tenant) .</span><span class="sxs-lookup"><span data-stu-id="190b4-106">Create a tenant or identify an existing B2C tenant for the app to use in the Azure portal by following the guidance in the [Create an AAD B2C tenant (Azure documentation)](/azure/active-directory-b2c/tutorial-create-tenant) article.</span></span>

<span data-ttu-id="190b4-107">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="190b4-107">Record the following information:</span></span>

* <span data-ttu-id="190b4-108">AAD B2C instance (par exemple, `https://contoso.b2clogin.com/` , qui comprend la barre oblique finale) : l’instance est le schéma et l’hôte d’une inscription d’application Azure B2C, que vous pouvez trouver en ouvrant la fenêtre **points de terminaison** à partir de la page **inscriptions d’applications** de la portail Azure.</span><span class="sxs-lookup"><span data-stu-id="190b4-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="190b4-109">AAD B2C domaine principal/éditeur/locataire (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.</span><span class="sxs-lookup"><span data-stu-id="190b4-109">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="190b4-110">Inscrire une application AAD B2C (aide connexe dans la documentation Azure : [Didacticiel : inscrire une application dans Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications)) :</span><span class="sxs-lookup"><span data-stu-id="190b4-110">Register an AAD B2C app (related guidance in the Azure documentation: [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications)):</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="190b4-111">Dans **Azure Active Directory** > **inscriptions d’applications** , sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="190b4-111">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="190b4-112">Fournissez un **nom** pour l’application (par exemple, **:::no-loc(Blazor)::: AAD B2C autonome** ).</span><span class="sxs-lookup"><span data-stu-id="190b4-112">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD B2C** ).</span></span>
1. <span data-ttu-id="190b4-113">Pour les **types de comptes pris en charge** , sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="190b4-113">For **Supported account types** , select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="190b4-114">Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="190b4-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="190b4-115">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="190b4-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="190b4-116">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="190b4-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="190b4-117">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="190b4-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="190b4-118">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="190b4-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="190b4-119">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="190b4-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="190b4-120">Vérifiez que **Permissions** > **les autorisations accordent le consentement de l’administrateur à OpenID et offline_access autorisations** sont sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="190b4-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="190b4-121">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="190b4-121">Select **Register**.</span></span>

<span data-ttu-id="190b4-122">Enregistrez l’ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="190b4-122">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="190b4-123">Dans configurations de plateforme **d’authentification** , > **Platform configurations** > **application à page unique (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="190b4-123">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="190b4-124">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="190b4-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="190b4-125">Pour **octroi implicite** , assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="190b4-125">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="190b4-126">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="190b4-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="190b4-127">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="190b4-127">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="190b4-128">Dans **Azure Active Directory** > **inscriptions d’applications** , sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="190b4-128">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="190b4-129">Fournissez un **nom** pour l’application (par exemple, **:::no-loc(Blazor)::: AAD B2C autonome** ).</span><span class="sxs-lookup"><span data-stu-id="190b4-129">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Standalone AAD B2C** ).</span></span>
1. <span data-ttu-id="190b4-130">Pour les **types de comptes pris en charge** , sélectionnez l’option multi-locataire : **comptes dans n’importe quel annuaire d’organisation ou n’importe quel fournisseur d’identité. Pour authentifier les utilisateurs avec Azure AD B2C.**</span><span class="sxs-lookup"><span data-stu-id="190b4-130">For **Supported account types** , select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="190b4-131">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="190b4-131">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="190b4-132">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="190b4-132">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="190b4-133">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="190b4-133">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="190b4-134">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="190b4-134">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="190b4-135">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="190b4-135">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="190b4-136">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="190b4-136">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="190b4-137">Vérifiez que **Permissions** > **les autorisations accordent le consentement de l’administrateur à OpenID et offline_access autorisations** sont sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="190b4-137">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="190b4-138">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="190b4-138">Select **Register**.</span></span>

<span data-ttu-id="190b4-139">Enregistrez l’ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="190b4-139">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="190b4-140">Dans **Authentication** le > **Platform configurations** > **site Web** configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="190b4-140">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="190b4-141">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="190b4-141">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="190b4-142">Pour **octroi implicite** , activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="190b4-142">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="190b4-143">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="190b4-143">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="190b4-144">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="190b4-144">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="190b4-145">Dans la **page** d'  >  **Azure ad B2C** des  >  **flux utilisateur** :</span><span class="sxs-lookup"><span data-stu-id="190b4-145">In **Home** > **Azure AD B2C** > **User flows** :</span></span>

[<span data-ttu-id="190b4-146">Créer un flux d’utilisateur d’inscription et de connexion</span><span class="sxs-lookup"><span data-stu-id="190b4-146">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="190b4-147">Au minimum, sélectionnez l' **Application claims**  >  attribut utilisateur **nom complet** des revendications d’application pour remplir le `context.User.:::no-loc(Identity):::.Name` dans le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="190b4-147">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.:::no-loc(Identity):::.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="190b4-148">Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="190b4-148">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="190b4-149">Dans un dossier vide, remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="190b4-149">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="190b4-150">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="190b4-150">Placeholder</span></span>                   | <span data-ttu-id="190b4-151">Nom du portail Azure</span><span class="sxs-lookup"><span data-stu-id="190b4-151">Azure portal name</span></span>               | <span data-ttu-id="190b4-152">Exemple</span><span class="sxs-lookup"><span data-stu-id="190b4-152">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="190b4-153">Instance</span><span class="sxs-lookup"><span data-stu-id="190b4-153">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `:::no-loc(Blazor):::Sample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="190b4-154">ID d’application (client)</span><span class="sxs-lookup"><span data-stu-id="190b4-154">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="190b4-155">Workflow d’inscription/de connexion</span><span class="sxs-lookup"><span data-stu-id="190b4-155">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="190b4-156">Domaine principal/serveur de publication/locataire</span><span class="sxs-lookup"><span data-stu-id="190b4-156">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="190b4-157">L’emplacement de sortie spécifié avec l’option `-o|--output` crée un dossier de projet s’il n’existe pas et devient partie intégrante du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="190b4-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="190b4-158">Dans le Portail Azure, l' **URI de redirection** de la configuration de plateforme de l’application est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="190b4-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="190b4-159">Si l’application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="190b4-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="190b4-160">Si le port n’a pas été configuré précédemment avec le port connu de l’application, revenez à l’inscription de l’application dans la Portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="190b4-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="190b4-161">Après avoir créé l’application, vous devez être en mesure d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="190b4-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="190b4-162">Connectez-vous à l’application à l’aide d’un compte d’utilisateur AAD.</span><span class="sxs-lookup"><span data-stu-id="190b4-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="190b4-163">Demander des jetons d’accès pour les API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="190b4-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="190b4-164">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="190b4-164">For more information, see:</span></span>
  * [<span data-ttu-id="190b4-165">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="190b4-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="190b4-166">[Démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="190b4-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="190b4-167">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="190b4-167">Authentication package</span></span>

<span data-ttu-id="190b4-168">Quand une application est créée pour utiliser un compte B2C individuel ( `IndividualB2C` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="190b4-168">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="190b4-169">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="190b4-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="190b4-170">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="190b4-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="190b4-171">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="190b4-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="190b4-172">Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.</span><span class="sxs-lookup"><span data-stu-id="190b4-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="190b4-173">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="190b4-173">Authentication service support</span></span>

<span data-ttu-id="190b4-174">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package.</span><span class="sxs-lookup"><span data-stu-id="190b4-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="190b4-175">Cette méthode configure tous les services requis pour que l’application interagisse avec le :::no-loc(Identity)::: fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="190b4-175">This method sets up all of the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="190b4-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="190b4-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="190b4-177">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="190b4-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="190b4-178">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="190b4-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="190b4-179">La configuration est fournie par le `wwwroot/:::no-loc(appsettings.json):::` fichier :</span><span class="sxs-lookup"><span data-stu-id="190b4-179">Configuration is supplied by the `wwwroot/:::no-loc(appsettings.json):::` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="190b4-180">Exemple :</span><span class="sxs-lookup"><span data-stu-id="190b4-180">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="190b4-181">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="190b4-181">Access token scopes</span></span>

<span data-ttu-id="190b4-182">Le :::no-loc(Blazor WebAssembly)::: modèle ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="190b4-182">The :::no-loc(Blazor WebAssembly)::: template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="190b4-183">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par défaut du <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="190b4-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="190b4-184">Spécifier des étendues supplémentaires avec `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="190b4-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="190b4-185">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="190b4-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="190b4-186">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="190b4-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="190b4-187">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="190b4-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="190b4-188">Mode de connexion</span><span class="sxs-lookup"><span data-stu-id="190b4-188">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="190b4-189">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="190b4-189">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="190b4-190">Page d'index</span><span class="sxs-lookup"><span data-stu-id="190b4-190">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="190b4-191">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="190b4-191">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="190b4-192">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="190b4-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="190b4-193">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="190b4-193">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="190b4-194">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="190b4-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="190b4-195">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="190b4-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="190b4-196">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="190b4-196">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="190b4-197">Tutoriel : Créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="190b4-197">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="190b4-198">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="190b4-198">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
