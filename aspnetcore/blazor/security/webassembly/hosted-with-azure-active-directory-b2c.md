---
title: Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory B2C
author: guardrex
description: Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core hébergée avec Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: dac2203d63b2d924ee6ae4f7012e9c33739e6213
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97792070"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="a2e00-103">Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="a2e00-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="a2e00-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a2e00-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a2e00-105">Cet article explique comment créer une [ Blazor WebAssembly application hébergée](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="a2e00-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="a2e00-106">Inscrire des applications dans AAD B2C et créer une solution</span><span class="sxs-lookup"><span data-stu-id="a2e00-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a2e00-107">Créer un locataire</span><span class="sxs-lookup"><span data-stu-id="a2e00-107">Create a tenant</span></span>

<span data-ttu-id="a2e00-108">Suivez les instructions du [Didacticiel : créer un locataire Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant) pour créer un locataire AAD B2C.</span><span class="sxs-lookup"><span data-stu-id="a2e00-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span> <span data-ttu-id="a2e00-109">Revenez à cet article immédiatement après avoir créé ou identifié un locataire à utiliser.</span><span class="sxs-lookup"><span data-stu-id="a2e00-109">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="a2e00-110">Enregistrez l’instance de AAD B2C (par exemple, `https://contoso.b2clogin.com/` , qui comprend la barre oblique finale).</span><span class="sxs-lookup"><span data-stu-id="a2e00-110">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="a2e00-111">L’instance est le schéma et l’hôte d’une inscription d’application Azure B2C, que vous pouvez trouver en ouvrant la fenêtre **points de terminaison** à partir de la page **inscriptions d’applications** du portail Azure.</span><span class="sxs-lookup"><span data-stu-id="a2e00-111">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a2e00-112">Inscrire une application API serveur</span><span class="sxs-lookup"><span data-stu-id="a2e00-112">Register a server API app</span></span>

<span data-ttu-id="a2e00-113">Inscrire une application AAD B2C pour l' *application API serveur*:</span><span class="sxs-lookup"><span data-stu-id="a2e00-113">Register an AAD B2C app for the *Server API app*:</span></span>

1. <span data-ttu-id="a2e00-114">Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-114">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a2e00-115">Fournissez un **nom** pour l’application (par exemple, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="a2e00-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="a2e00-116">Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel fournisseur d’identité ou annuaire d’organisation (pour authentifier les utilisateurs avec des flux utilisateur)**</span><span class="sxs-lookup"><span data-stu-id="a2e00-116">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="a2e00-117">L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="a2e00-117">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a2e00-118">Vérifiez que   >  **les autorisations accordent le consentement de l’administrateur à OpenID et offline_access autorisations** sont sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="a2e00-118">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="a2e00-119">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-119">Select **Register**.</span></span>

<span data-ttu-id="a2e00-120">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="a2e00-120">Record the following information:</span></span>

* <span data-ttu-id="a2e00-121">*Application API serveur* ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="a2e00-121">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="a2e00-122">Le domaine principal/serveur de publication/client AAD (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.</span><span class="sxs-lookup"><span data-stu-id="a2e00-122">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="a2e00-123">Dans **exposer une API**:</span><span class="sxs-lookup"><span data-stu-id="a2e00-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="a2e00-124">sélectionner **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a2e00-125">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a2e00-126">Spécifiez un **nom d’étendue** (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a2e00-127">Spécifiez un **nom d’affichage du consentement administrateur** (par exemple, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a2e00-128">Fournissez une **Description du consentement** de l’administrateur (par exemple, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a2e00-129">Confirmez que l' **État** est défini sur **activé**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a2e00-130">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-130">Select **Add scope**.</span></span>

<span data-ttu-id="a2e00-131">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="a2e00-131">Record the following information:</span></span>

* <span data-ttu-id="a2e00-132">URI ID d’application (par exemple,, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ou la valeur personnalisée que vous avez fournie)</span><span class="sxs-lookup"><span data-stu-id="a2e00-132">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="a2e00-133">Nom de l’étendue (par exemple, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="a2e00-133">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a2e00-134">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="a2e00-134">Register a client app</span></span>

<span data-ttu-id="a2e00-135">Inscrire une application AAD B2C pour l' *application cliente*:</span><span class="sxs-lookup"><span data-stu-id="a2e00-135">Register an AAD B2C app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="a2e00-136">Dans **Azure Active Directory** > **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a2e00-137">Fournissez un **nom** pour l’application (par exemple, **Blazor client AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="a2e00-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="a2e00-138">Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel fournisseur d’identité ou annuaire d’organisation (pour authentifier les utilisateurs avec des flux utilisateur)**</span><span class="sxs-lookup"><span data-stu-id="a2e00-138">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="a2e00-139">Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="a2e00-139">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a2e00-140">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="a2e00-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a2e00-141">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="a2e00-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a2e00-142">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les *`Server`* Propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="a2e00-142">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a2e00-143">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="a2e00-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a2e00-144">Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="a2e00-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a2e00-145">Vérifiez que  > **les autorisations accordent le consentement de l’administrateur à OpenID et offline_access autorisations** sont sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="a2e00-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="a2e00-146">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-146">Select **Register**.</span></span>

1. <span data-ttu-id="a2e00-147">Enregistrez l’ID de l’application (client) (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-147">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="a2e00-148">Dans configurations de plateforme **d’authentification** , >  > **application à page unique (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="a2e00-148">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="a2e00-149">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="a2e00-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a2e00-150">Pour **octroi implicite**, assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="a2e00-150">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="a2e00-151">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="a2e00-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a2e00-152">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-152">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="a2e00-153">Dans **Azure Active Directory** > **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-153">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="a2e00-154">Fournissez un **nom** pour l’application (par exemple, **Blazor client AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="a2e00-154">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="a2e00-155">Pour les **types de comptes pris en charge**, sélectionnez l’option multi-locataire : **comptes dans n’importe quel fournisseur d’identité ou annuaire d’organisation (pour authentifier les utilisateurs avec des flux utilisateur)**</span><span class="sxs-lookup"><span data-stu-id="a2e00-155">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="a2e00-156">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="a2e00-156">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a2e00-157">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="a2e00-157">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a2e00-158">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="a2e00-158">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a2e00-159">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les *`Server`* Propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="a2e00-159">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a2e00-160">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="a2e00-160">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a2e00-161">Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="a2e00-161">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a2e00-162">Vérifiez que  > **les autorisations accordent le consentement de l’administrateur à OpenID et offline_access autorisations** sont sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="a2e00-162">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="a2e00-163">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-163">Select **Register**.</span></span>

<span data-ttu-id="a2e00-164">Enregistrez l’ID de l’application (client) (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-164">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="a2e00-165">Dans  le >  > **site Web** configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="a2e00-165">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a2e00-166">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="a2e00-166">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a2e00-167">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-167">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a2e00-168">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="a2e00-168">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a2e00-169">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-169">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="a2e00-170">Dans **autorisations d’API**:</span><span class="sxs-lookup"><span data-stu-id="a2e00-170">In **API permissions**:</span></span>

1. <span data-ttu-id="a2e00-171">Sélectionnez **Ajouter une autorisation** suivi de **mes API**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-171">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a2e00-172">Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="a2e00-172">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="a2e00-173">Ouvrez la liste des **API** .</span><span class="sxs-lookup"><span data-stu-id="a2e00-173">Open the **API** list.</span></span>
1. <span data-ttu-id="a2e00-174">Activez l’accès à l’API (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-174">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a2e00-175">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="a2e00-175">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a2e00-176">Sélectionnez le bouton **accorder le consentement de l’administrateur pour {nom du locataire}** .</span><span class="sxs-lookup"><span data-stu-id="a2e00-176">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="a2e00-177">Sélectionnez **Oui** pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="a2e00-177">Select **Yes** to confirm.</span></span>

<span data-ttu-id="a2e00-178">Dans la **page** d'  >  **Azure ad B2C** des  >  **flux utilisateur**:</span><span class="sxs-lookup"><span data-stu-id="a2e00-178">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="a2e00-179">Créer un flux d’utilisateur d’inscription et de connexion</span><span class="sxs-lookup"><span data-stu-id="a2e00-179">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="a2e00-180">Au minimum, sélectionnez l'   >  attribut utilisateur **nom complet** des revendications d’application pour remplir le `context.User.Identity.Name` dans le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-180">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="a2e00-181">Notez le nom du workflow d’inscription et de connexion de l’utilisateur créé pour l’application (par exemple, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-181">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a2e00-182">Créer l’application</span><span class="sxs-lookup"><span data-stu-id="a2e00-182">Create the app</span></span>

<span data-ttu-id="a2e00-183">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="a2e00-183">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="a2e00-184">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="a2e00-184">Placeholder</span></span>                   | <span data-ttu-id="a2e00-185">Nom du portail Azure</span><span class="sxs-lookup"><span data-stu-id="a2e00-185">Azure portal name</span></span>                                     | <span data-ttu-id="a2e00-186">Exemple</span><span class="sxs-lookup"><span data-stu-id="a2e00-186">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="a2e00-187">Instance</span><span class="sxs-lookup"><span data-stu-id="a2e00-187">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="a2e00-188">ID de l’application (client) pour l' *`Client`* application</span><span class="sxs-lookup"><span data-stu-id="a2e00-188">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="a2e00-189">Nom de l’étendue</span><span class="sxs-lookup"><span data-stu-id="a2e00-189">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="a2e00-190">ID de l’application (client) pour l’application *API serveur*</span><span class="sxs-lookup"><span data-stu-id="a2e00-190">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="a2e00-191">URI d’ID d’application&dagger;</span><span class="sxs-lookup"><span data-stu-id="a2e00-191">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="a2e00-192">Workflow d’inscription/de connexion</span><span class="sxs-lookup"><span data-stu-id="a2e00-192">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="a2e00-193">Domaine principal/serveur de publication/locataire</span><span class="sxs-lookup"><span data-stu-id="a2e00-193">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="a2e00-194">&dagger;Le Blazor WebAssembly modèle ajoute automatiquement un schéma de `api://` à l’argument d’URI ID d’application passé dans la `dotnet new` commande.</span><span class="sxs-lookup"><span data-stu-id="a2e00-194">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="a2e00-195">Lorsque vous fournissez l’URI ID d’application pour l' `{SERVER API APP ID URI}` espace réservé et si le schéma est `api://` , supprimez le schéma ( `api://` ) de l’argument, comme le montre l’exemple de valeur dans le tableau précédent.</span><span class="sxs-lookup"><span data-stu-id="a2e00-195">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="a2e00-196">Si l’URI ID d’application est une valeur personnalisée ou s’il en existe un autre (par exemple, `https://` pour un domaine du serveur de publication non approuvé similaire à `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), vous devez mettre à jour manuellement l’URI de l’étendue par défaut et supprimer le `api://` schéma une fois que l' *`Client`* application a été créée par le modèle.</span><span class="sxs-lookup"><span data-stu-id="a2e00-196">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="a2e00-197">Pour plus d’informations, consultez la remarque dans la section [étendues de jetons d’accès](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="a2e00-197">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="a2e00-198">Le Blazor WebAssembly modèle peut être modifié dans une version ultérieure de ASP.net Core pour résoudre ces scénarios.</span><span class="sxs-lookup"><span data-stu-id="a2e00-198">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="a2e00-199">Pour plus d’informations, consultez [schéma double pour l’URI ID d’application avec Blazor modèle WASM (hébergé, unique org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="a2e00-199">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="a2e00-200">L’emplacement de sortie spécifié avec l’option `-o|--output` crée un dossier de projet s’il n’existe pas et devient partie intégrante du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="a2e00-200">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a2e00-201">L’étendue configurée par le Blazor modèle hébergé peut avoir un hôte URI ID d’application répété.</span><span class="sxs-lookup"><span data-stu-id="a2e00-201">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="a2e00-202">Confirmez que l’étendue configurée pour la `DefaultAccessTokenScopes` collection est correcte dans `Program.Main` ( `Program.cs` ) de l' *`Client`* application.</span><span class="sxs-lookup"><span data-stu-id="a2e00-202">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="a2e00-203">Dans le Portail Azure, l' *`Client`* **URI de redirection** de la configuration de plateforme de l’application est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="a2e00-203">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a2e00-204">Si l' *`Client`* application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l' *application API serveur* dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="a2e00-204">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a2e00-205">Si le port n’a pas été configuré précédemment avec le *`Client`* port connu de l’application, revenez à l’inscription de l' *`Client`* application dans la portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="a2e00-205">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a2e00-206">*`Server`* configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="a2e00-206">*`Server`* app configuration</span></span>

<span data-ttu-id="a2e00-207">*Cette section se rapporte à l’application de la solution **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="a2e00-207">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a2e00-208">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="a2e00-208">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a2e00-209">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core les API Web avec la Identity plate-forme Microsoft est assurée par les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="a2e00-209">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="a2e00-210">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="a2e00-210">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a2e00-211">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API Web est assurée par le [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) Package :</span><span class="sxs-lookup"><span data-stu-id="a2e00-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="a2e00-212">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="a2e00-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="a2e00-213">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="a2e00-213">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a2e00-214">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="a2e00-214">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a2e00-215">La <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> méthode Configure les services pour protéger l’API Web avec Microsoft Identity Platform v 2.0.</span><span class="sxs-lookup"><span data-stu-id="a2e00-215">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="a2e00-216">Cette méthode attend une `AzureAdB2C` section dans la configuration de l’application avec les paramètres nécessaires pour initialiser les options d’authentification.</span><span class="sxs-lookup"><span data-stu-id="a2e00-216">This method expects an `AzureAdB2C` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a2e00-217">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="a2e00-217">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a2e00-218">La <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory B2C :</span><span class="sxs-lookup"><span data-stu-id="a2e00-218">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

::: moniker-end

<span data-ttu-id="a2e00-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> et <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Assurez-vous que :</span><span class="sxs-lookup"><span data-stu-id="a2e00-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="a2e00-220">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="a2e00-220">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a2e00-221">Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="a2e00-221">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="a2e00-222">Utilisateur. Identity . Nomme</span><span class="sxs-lookup"><span data-stu-id="a2e00-222">User.Identity.Name</span></span>

<span data-ttu-id="a2e00-223">Par défaut, le `User.Identity.Name` n’est pas rempli.</span><span class="sxs-lookup"><span data-stu-id="a2e00-223">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="a2e00-224">Pour configurer l’application afin qu’elle reçoive la valeur du `name` type de revendication :</span><span class="sxs-lookup"><span data-stu-id="a2e00-224">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="a2e00-225">Ajoutez un espace de noms pour <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> à `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="a2e00-225">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="a2e00-226">Configurez le <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a2e00-226">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="a2e00-227">Configurez le <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a2e00-227">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="a2e00-228">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="a2e00-228">App settings</span></span>

<span data-ttu-id="a2e00-229">Le `appsettings.json` fichier contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès.</span><span class="sxs-lookup"><span data-stu-id="a2e00-229">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="a2e00-230">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a2e00-230">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="a2e00-231">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="a2e00-231">WeatherForecast controller</span></span>

<span data-ttu-id="a2e00-232">Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="a2e00-232">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="a2e00-233">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="a2e00-233">It's **important** to understand that:</span></span>

* <span data-ttu-id="a2e00-234">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="a2e00-234">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a2e00-235">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut utilisé dans l' Blazor WebAssembly application sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="a2e00-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="a2e00-236">*`Client`* configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="a2e00-236">*`Client`* app configuration</span></span>

<span data-ttu-id="a2e00-237">*Cette section se rapporte à l’application de la solution **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="a2e00-237">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a2e00-238">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="a2e00-238">Authentication package</span></span>

<span data-ttu-id="a2e00-239">Quand une application est créée pour utiliser un compte B2C individuel ( `IndividualB2C` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-239">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="a2e00-240">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="a2e00-240">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a2e00-241">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="a2e00-241">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="a2e00-242">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="a2e00-242">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="a2e00-243">Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.</span><span class="sxs-lookup"><span data-stu-id="a2e00-243">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a2e00-244">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="a2e00-244">Authentication service support</span></span>

<span data-ttu-id="a2e00-245">La prise en charge des <xref:System.Net.Http.HttpClient> instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="a2e00-245">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="a2e00-246">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a2e00-246">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="a2e00-247">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-247">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="a2e00-248">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package.</span><span class="sxs-lookup"><span data-stu-id="a2e00-248">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="a2e00-249">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="a2e00-249">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a2e00-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a2e00-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a2e00-251">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="a2e00-251">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a2e00-252">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="a2e00-252">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="a2e00-253">La configuration est fournie par le `wwwroot/appsettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="a2e00-253">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="a2e00-254">Exemple :</span><span class="sxs-lookup"><span data-stu-id="a2e00-254">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="a2e00-255">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="a2e00-255">Access token scopes</span></span>

<span data-ttu-id="a2e00-256">Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="a2e00-256">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a2e00-257">Inclus par défaut dans la demande de connexion.</span><span class="sxs-lookup"><span data-stu-id="a2e00-257">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a2e00-258">Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="a2e00-258">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a2e00-259">Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles.</span><span class="sxs-lookup"><span data-stu-id="a2e00-259">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a2e00-260">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :</span><span class="sxs-lookup"><span data-stu-id="a2e00-260">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a2e00-261">Le Blazor WebAssembly modèle ajoute automatiquement un schéma de `api://` à l’argument d’URI ID d’application passé dans la `dotnet new` commande.</span><span class="sxs-lookup"><span data-stu-id="a2e00-261">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="a2e00-262">Lors de la génération d’une application à partir du Blazor modèle de projet, vérifiez que la valeur de l’étendue du jeton d’accès par défaut utilise la valeur d’URI ID d’application personnalisée correcte que vous avez fournie dans le portail Azure ou une valeur avec l' **un** des formats suivants :</span><span class="sxs-lookup"><span data-stu-id="a2e00-262">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="a2e00-263">Lorsque le domaine de l’éditeur de l’annuaire est **approuvé**, l’étendue du jeton d’accès par défaut est généralement une valeur similaire à celle de l’exemple suivant, où `API.Access` est le nom de l’étendue par défaut :</span><span class="sxs-lookup"><span data-stu-id="a2e00-263">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="a2e00-264">Inspectez la valeur d’un schéma double ( `api://api://...` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-264">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="a2e00-265">Si un double schéma est présent, supprimez le premier `api://` schéma de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a2e00-265">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="a2e00-266">Lorsque le domaine du serveur de publication de l’annuaire n’est pas **approuvé**, l’étendue du jeton d’accès par défaut est généralement une valeur similaire à celle de l’exemple suivant, où `API.Access` est le nom de l’étendue par défaut :</span><span class="sxs-lookup"><span data-stu-id="a2e00-266">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="a2e00-267">Inspectez la valeur d’un `api://` schéma supplémentaire ( `api://https://contoso.onmicrosoft.com/...` ).</span><span class="sxs-lookup"><span data-stu-id="a2e00-267">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="a2e00-268">Si un `api://` schéma supplémentaire est présent, supprimez le `api://` schéma de la valeur.</span><span class="sxs-lookup"><span data-stu-id="a2e00-268">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="a2e00-269">Le Blazor WebAssembly modèle peut être modifié dans une version ultérieure de ASP.net Core pour résoudre ces scénarios.</span><span class="sxs-lookup"><span data-stu-id="a2e00-269">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="a2e00-270">Pour plus d’informations, consultez [schéma double pour l’URI ID d’application avec Blazor modèle WASM (hébergé, unique org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="a2e00-270">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="a2e00-271">Spécifier des étendues supplémentaires avec `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="a2e00-271">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="a2e00-272">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="a2e00-272">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a2e00-273">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a2e00-273">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a2e00-274">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="a2e00-274">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="a2e00-275">Mode de connexion</span><span class="sxs-lookup"><span data-stu-id="a2e00-275">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="a2e00-276">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="a2e00-276">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a2e00-277">Page d'index</span><span class="sxs-lookup"><span data-stu-id="a2e00-277">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="a2e00-278">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="a2e00-278">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a2e00-279">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="a2e00-279">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a2e00-280">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="a2e00-280">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a2e00-281">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="a2e00-281">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a2e00-282">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="a2e00-282">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a2e00-283">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="a2e00-283">Run the app</span></span>

<span data-ttu-id="a2e00-284">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="a2e00-284">Run the app from the Server project.</span></span> <span data-ttu-id="a2e00-285">Lorsque vous utilisez Visual Studio, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="a2e00-285">When using Visual Studio, either:</span></span>

* <span data-ttu-id="a2e00-286">Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="a2e00-286">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="a2e00-287">Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="a2e00-287">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a2e00-288">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="a2e00-288">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a2e00-289">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="a2e00-289">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="a2e00-290">Tutoriel : Créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="a2e00-290">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="a2e00-291">Tutoriel : Inscrire une application dans Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="a2e00-291">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="a2e00-292">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="a2e00-292">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
