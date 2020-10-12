---
title: Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory
author: guardrex
description: Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core hébergée avec Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: e6f514793a2efde120f70ac58f4ad4be7516ada7
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900838"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="028ef-103">Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="028ef-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="028ef-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="028ef-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="028ef-105">Cet article explique comment créer une [ Blazor WebAssembly application hébergée](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="028ef-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="028ef-106">Inscrire des applications dans AAD et créer une solution</span><span class="sxs-lookup"><span data-stu-id="028ef-106">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="028ef-107">Créer un client</span><span class="sxs-lookup"><span data-stu-id="028ef-107">Create a tenant</span></span>

<span data-ttu-id="028ef-108">Suivez les instructions de [démarrage rapide : configurer un locataire](/azure/active-directory/develop/quickstart-create-new-tenant) pour créer un locataire dans AAD.</span><span class="sxs-lookup"><span data-stu-id="028ef-108">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="028ef-109">Inscrire une application API serveur</span><span class="sxs-lookup"><span data-stu-id="028ef-109">Register a server API app</span></span>

<span data-ttu-id="028ef-110">Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *application API serveur* , puis procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="028ef-110">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="028ef-111">Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="028ef-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="028ef-112">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="028ef-112">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="028ef-113">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="028ef-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="028ef-114">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="028ef-114">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="028ef-115">L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="028ef-115">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="028ef-116">Désactivez **la case**  >  à cocher**accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="028ef-116">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="028ef-117">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="028ef-117">Select **Register**.</span></span>

<span data-ttu-id="028ef-118">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="028ef-118">Record the following information:</span></span>

* <span data-ttu-id="028ef-119">*Application API serveur* ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="028ef-119">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="028ef-120">ID de répertoire (locataire) (par exemple, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="028ef-120">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="028ef-121">Le domaine principal/serveur de publication/client AAD (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.</span><span class="sxs-lookup"><span data-stu-id="028ef-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="028ef-122">Dans **autorisations d’API**, supprimez l’autorisation **Microsoft Graph**  >  **User. Read** , car l’application ne nécessite pas d’accès de connexion ou de profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="028ef-122">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="028ef-123">Dans **exposer une API**:</span><span class="sxs-lookup"><span data-stu-id="028ef-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="028ef-124">sélectionner **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="028ef-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="028ef-125">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="028ef-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="028ef-126">Spécifiez un **nom d’étendue** (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="028ef-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="028ef-127">Spécifiez un **nom d’affichage du consentement administrateur** (par exemple, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="028ef-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="028ef-128">Fournissez une **Description du consentement** de l’administrateur (par exemple, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="028ef-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="028ef-129">Confirmez que l' **État** est défini sur **activé**.</span><span class="sxs-lookup"><span data-stu-id="028ef-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="028ef-130">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="028ef-130">Select **Add scope**.</span></span>

<span data-ttu-id="028ef-131">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="028ef-131">Record the following information:</span></span>

* <span data-ttu-id="028ef-132">URI ID d’application (par exemple,, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ou la valeur personnalisée que vous fournissez)</span><span class="sxs-lookup"><span data-stu-id="028ef-132">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="028ef-133">Nom de l’étendue (par exemple, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="028ef-133">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="028ef-134">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="028ef-134">Register a client app</span></span>

<span data-ttu-id="028ef-135">Suivez les instructions de [démarrage rapide : inscrire une application auprès de la plateforme Microsoft Identity](/azure/active-directory/develop/quickstart-register-app) et des rubriques Azure AAD suivantes pour inscrire une application AAD pour l' *`Client`* application, puis procédez comme suit :</span><span class="sxs-lookup"><span data-stu-id="028ef-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="028ef-136">Dans **Azure Active Directory** > **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="028ef-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="028ef-137">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="028ef-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="028ef-138">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="028ef-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="028ef-139">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="028ef-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="028ef-140">Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="028ef-140">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="028ef-141">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="028ef-141">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="028ef-142">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="028ef-142">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="028ef-143">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les *`Server`* Propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="028ef-143">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="028ef-144">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="028ef-144">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="028ef-145">Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="028ef-145">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="028ef-146">Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="028ef-146">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="028ef-147">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="028ef-147">Select **Register**.</span></span>

<span data-ttu-id="028ef-148">Enregistrez l' *`Client`* ID de l’application (client) de l’application (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="028ef-148">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="028ef-149">Dans configurations de plateforme **d’authentification** , > **Platform configurations** > **application à page unique (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="028ef-149">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="028ef-150">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="028ef-150">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="028ef-151">Pour **octroi implicite**, assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="028ef-151">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="028ef-152">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="028ef-152">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="028ef-153">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="028ef-153">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="028ef-154">Dans **Azure Active Directory** > **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="028ef-154">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="028ef-155">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor client AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="028ef-155">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="028ef-156">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="028ef-156">Choose a **Supported account types**.</span></span> <span data-ttu-id="028ef-157">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="028ef-157">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="028ef-158">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="028ef-158">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="028ef-159">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="028ef-159">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="028ef-160">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="028ef-160">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="028ef-161">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les *`Server`* Propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="028ef-161">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="028ef-162">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="028ef-162">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="028ef-163">Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="028ef-163">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="028ef-164">Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="028ef-164">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="028ef-165">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="028ef-165">Select **Register**.</span></span>

<span data-ttu-id="028ef-166">Enregistrez l' *`Client`* ID de l’application (client) de l’application (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="028ef-166">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="028ef-167">Dans **Authentication** le > **Platform configurations** > **site Web**configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="028ef-167">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="028ef-168">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="028ef-168">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="028ef-169">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="028ef-169">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="028ef-170">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="028ef-170">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="028ef-171">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="028ef-171">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="028ef-172">Dans **autorisations d’API**:</span><span class="sxs-lookup"><span data-stu-id="028ef-172">In **API permissions**:</span></span>

1. <span data-ttu-id="028ef-173">Vérifiez que l’application a **Microsoft Graph**  >  autorisation**User. Read** .</span><span class="sxs-lookup"><span data-stu-id="028ef-173">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="028ef-174">Sélectionnez **Ajouter une autorisation** suivi de **mes API**.</span><span class="sxs-lookup"><span data-stu-id="028ef-174">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="028ef-175">Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, \*\* Blazor Server AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="028ef-175">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="028ef-176">Ouvrez la liste des **API** .</span><span class="sxs-lookup"><span data-stu-id="028ef-176">Open the **API** list.</span></span>
1. <span data-ttu-id="028ef-177">Activez l’accès à l’API (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="028ef-177">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="028ef-178">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="028ef-178">Select **Add permissions**.</span></span>
1. <span data-ttu-id="028ef-179">Sélectionnez le bouton **accorder le consentement de l’administrateur pour {nom du locataire}** .</span><span class="sxs-lookup"><span data-stu-id="028ef-179">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="028ef-180">Cliquez sur **Oui** pour confirmer la suppression.</span><span class="sxs-lookup"><span data-stu-id="028ef-180">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="028ef-181">Créer l’application</span><span class="sxs-lookup"><span data-stu-id="028ef-181">Create the app</span></span>

<span data-ttu-id="028ef-182">Dans un dossier vide, remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="028ef-182">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="028ef-183">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="028ef-183">Placeholder</span></span>                  | <span data-ttu-id="028ef-184">Nom du portail Azure</span><span class="sxs-lookup"><span data-stu-id="028ef-184">Azure portal name</span></span>                                     | <span data-ttu-id="028ef-185">Exemple</span><span class="sxs-lookup"><span data-stu-id="028ef-185">Example</span></span>                                      |
| ---------------------------- | ----------------------------------------------------- | -------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                               |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="028ef-186">ID de l’application (client) pour l' *`Client`* application</span><span class="sxs-lookup"><span data-stu-id="028ef-186">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`       |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="028ef-187">Nom de l’étendue</span><span class="sxs-lookup"><span data-stu-id="028ef-187">Scope name</span></span>                                            | `API.Access`                                 |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="028ef-188">ID de l’application (client) pour l’application *API serveur*</span><span class="sxs-lookup"><span data-stu-id="028ef-188">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`       |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="028ef-189">URI d’ID d’application</span><span class="sxs-lookup"><span data-stu-id="028ef-189">Application ID URI</span></span>                                    | `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT DOMAIN}`            | <span data-ttu-id="028ef-190">Domaine principal/serveur de publication/locataire</span><span class="sxs-lookup"><span data-stu-id="028ef-190">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                    |
| `{TENANT ID}`                | <span data-ttu-id="028ef-191">ID de l’annuaire (locataire)</span><span class="sxs-lookup"><span data-stu-id="028ef-191">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`       |

<span data-ttu-id="028ef-192">L’emplacement de sortie spécifié avec l' `-o|--output` option crée un dossier de projet s’il n’existe pas et fait partie du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="028ef-192">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="028ef-193">Une modification de configuration peut être nécessaire lors de l’utilisation d’un client Azure avec un domaine d’éditeur non vérifié, qui est décrit dans la section paramètres de l' [application](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="028ef-193">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="028ef-194">Une modification de configuration peut être nécessaire lors de l’utilisation d’un client Azure avec un domaine d’éditeur non vérifié, qui est décrit dans la section [étendues de jeton d’accès](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="028ef-194">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="028ef-195">Dans le Portail Azure, l' *`Client`* **URI de redirection** de la configuration de plateforme de l’application est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="028ef-195">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="028ef-196">Si l' *`Client`* application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l' *application API serveur* dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="028ef-196">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="028ef-197">Si le port n’a pas été configuré précédemment avec le *`Client`* port connu de l’application, revenez à l’inscription de l' *`Client`* application dans la portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="028ef-197">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="028ef-198">*`Server`* configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="028ef-198">*`Server`* app configuration</span></span>

<span data-ttu-id="028ef-199">*Cette section se rapporte à l’application de la solution **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="028ef-199">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="028ef-200">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="028ef-200">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="028ef-201">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core les API Web avec la Identity plate-forme Microsoft est assurée par les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="028ef-201">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="028ef-202">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="028ef-202">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="028ef-203">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API Web est assurée par le [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) Package :</span><span class="sxs-lookup"><span data-stu-id="028ef-203">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="028ef-204">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="028ef-204">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="028ef-205">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="028ef-205">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="028ef-206">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="028ef-206">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="028ef-207">La <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> méthode Configure les services pour protéger l’API Web avec Microsoft Identity Platform v 2.0.</span><span class="sxs-lookup"><span data-stu-id="028ef-207">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="028ef-208">Cette méthode attend une `AzureAd` section dans la configuration de l’application avec les paramètres nécessaires pour initialiser les options d’authentification.</span><span class="sxs-lookup"><span data-stu-id="028ef-208">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="028ef-209">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="028ef-209">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="028ef-210">La <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory :</span><span class="sxs-lookup"><span data-stu-id="028ef-210">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="028ef-211"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> et <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Assurez-vous que :</span><span class="sxs-lookup"><span data-stu-id="028ef-211"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="028ef-212">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="028ef-212">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="028ef-213">Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="028ef-213">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="028ef-214">Utilisateur. Identity . Nomme</span><span class="sxs-lookup"><span data-stu-id="028ef-214">User.Identity.Name</span></span>

<span data-ttu-id="028ef-215">Par défaut, l' *`Server`* API d’application remplit `User.Identity.Name` la valeur du type de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` revendication (par exemple, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="028ef-215">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="028ef-216">Pour configurer l’application afin qu’elle reçoive la valeur du `name` type de revendication, configurez le <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="028ef-216">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="028ef-217">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="028ef-217">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="028ef-218">Le `appsettings.json` fichier contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès :</span><span class="sxs-lookup"><span data-stu-id="028ef-218">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="028ef-219">Exemple :</span><span class="sxs-lookup"><span data-stu-id="028ef-219">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="028ef-220">Le `appsettings.json` fichier contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès :</span><span class="sxs-lookup"><span data-stu-id="028ef-220">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="028ef-221">Exemple :</span><span class="sxs-lookup"><span data-stu-id="028ef-221">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="028ef-222">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="028ef-222">WeatherForecast controller</span></span>

<span data-ttu-id="028ef-223">Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="028ef-223">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="028ef-224">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="028ef-224">It's **important** to understand that:</span></span>

* <span data-ttu-id="028ef-225">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="028ef-225">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="028ef-226">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut utilisé dans l' Blazor WebAssembly application sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="028ef-226">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="028ef-227">*`Client`* configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="028ef-227">*`Client`* app configuration</span></span>

<span data-ttu-id="028ef-228">*Cette section se rapporte à l’application de la solution **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="028ef-228">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="028ef-229">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="028ef-229">Authentication package</span></span>

<span data-ttu-id="028ef-230">Quand une application est créée pour utiliser des comptes professionnels ou scolaires ( `SingleOrg` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="028ef-230">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="028ef-231">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="028ef-231">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="028ef-232">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="028ef-232">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="028ef-233">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="028ef-233">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="028ef-234">Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.</span><span class="sxs-lookup"><span data-stu-id="028ef-234">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="028ef-235">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="028ef-235">Authentication service support</span></span>

<span data-ttu-id="028ef-236">La prise en charge des <xref:System.Net.Http.HttpClient> instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="028ef-236">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="028ef-237">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="028ef-237">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="028ef-238">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="028ef-238">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="028ef-239">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package.</span><span class="sxs-lookup"><span data-stu-id="028ef-239">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="028ef-240">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="028ef-240">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="028ef-241">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="028ef-241">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="028ef-242">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="028ef-242">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="028ef-243">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="028ef-243">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="028ef-244">La configuration est fournie par le `wwwroot/appsettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="028ef-244">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="028ef-245">Exemple :</span><span class="sxs-lookup"><span data-stu-id="028ef-245">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="028ef-246">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="028ef-246">Access token scopes</span></span>

<span data-ttu-id="028ef-247">Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="028ef-247">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="028ef-248">Inclus par défaut dans la demande de connexion.</span><span class="sxs-lookup"><span data-stu-id="028ef-248">Included by default in the sign in request.</span></span>
* <span data-ttu-id="028ef-249">Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="028ef-249">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="028ef-250">Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles.</span><span class="sxs-lookup"><span data-stu-id="028ef-250">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="028ef-251">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :</span><span class="sxs-lookup"><span data-stu-id="028ef-251">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="028ef-252">Spécifier des étendues supplémentaires avec `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="028ef-252">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="028ef-253">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="028ef-253">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="028ef-254">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="028ef-254">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="028ef-255">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="028ef-255">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="028ef-256">Mode de connexion</span><span class="sxs-lookup"><span data-stu-id="028ef-256">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="028ef-257">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="028ef-257">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="028ef-258">Page d'index</span><span class="sxs-lookup"><span data-stu-id="028ef-258">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="028ef-259">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="028ef-259">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="028ef-260">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="028ef-260">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="028ef-261">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="028ef-261">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="028ef-262">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="028ef-262">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="028ef-263">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="028ef-263">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="028ef-264">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="028ef-264">Run the app</span></span>

<span data-ttu-id="028ef-265">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="028ef-265">Run the app from the Server project.</span></span> <span data-ttu-id="028ef-266">Lorsque vous utilisez Visual Studio, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="028ef-266">When using Visual Studio, either:</span></span>

* <span data-ttu-id="028ef-267">Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="028ef-267">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="028ef-268">Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="028ef-268">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="028ef-269">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="028ef-269">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="028ef-270">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="028ef-270">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="028ef-271">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="028ef-271">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
