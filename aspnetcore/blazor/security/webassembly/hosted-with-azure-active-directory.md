---
title: Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory
author: guardrex
description: Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core hébergée avec Azure Active Directory.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: f0c7723e8de7167b4fe5021ea1ca26e7cf198581
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710566"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="cbf73-103">Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="cbf73-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="cbf73-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cbf73-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cbf73-105">Cet article explique comment créer une [ Blazor WebAssembly application hébergée](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="cbf73-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="cbf73-106">Pour les Blazor WebAssembly applications créées dans Visual Studio qui sont configurées pour prendre en charge les comptes dans un annuaire d’organisation AAD, Visual Studio ne configure pas actuellement les projets de la solution ou les inscriptions d’application portail Azure correctement sur la génération de projet.</span><span class="sxs-lookup"><span data-stu-id="cbf73-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure the solution's projects or the Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="cbf73-107">Cela sera résolu dans une prochaine version de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cbf73-107">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="cbf73-108">Cet article explique comment créer la solution et les inscriptions d’Azure App Portal à l’aide de la commande CLI .NET `dotnet new` et en créant manuellement les inscriptions d’applications dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="cbf73-108">This article shows how to create the solution and Azure app portal registrations with the .NET CLI `dotnet new` command and by manually creating the app registrations in the Azure portal.</span></span>
>
> <span data-ttu-id="cbf73-109">Si vous préférez créer la solution et les inscriptions d’applications Azure avec Visual Studio avant la mise à jour de l’IDE, reportez-vous à **_chaque section de cet article_** et confirmez ou mettez à jour les configurations des applications et les inscriptions des applications une fois que Visual Studio a créé la solution.</span><span class="sxs-lookup"><span data-stu-id="cbf73-109">If you prefer to create the solution and Azure app registrations with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the apps' configurations and the apps' registrations after Visual Studio creates the solution.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="cbf73-110">Inscrire des applications dans AAD et créer une solution</span><span class="sxs-lookup"><span data-stu-id="cbf73-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="cbf73-111">Créer un locataire</span><span class="sxs-lookup"><span data-stu-id="cbf73-111">Create a tenant</span></span>

<span data-ttu-id="cbf73-112">Suivez les instructions de [démarrage rapide : configurer un locataire](/azure/active-directory/develop/quickstart-create-new-tenant) pour créer un locataire dans AAD.</span><span class="sxs-lookup"><span data-stu-id="cbf73-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="cbf73-113">Inscrire une application API serveur</span><span class="sxs-lookup"><span data-stu-id="cbf73-113">Register a server API app</span></span>

<span data-ttu-id="cbf73-114">Inscrire une application AAD pour l' *application API serveur*:</span><span class="sxs-lookup"><span data-stu-id="cbf73-114">Register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="cbf73-115">Dans **Azure Active Directory**  >  **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="cbf73-116">Fournissez un **nom** pour l’application (par exemple, **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="cbf73-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="cbf73-117">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="cbf73-118">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="cbf73-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="cbf73-119">L' *application API serveur* ne requiert pas d' **URI de redirection** dans ce scénario, laissez la liste déroulante définie sur **Web** et n’entrez pas d’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="cbf73-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="cbf73-120">Désactivez **la case**  >  à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="cbf73-121">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-121">Select **Register**.</span></span>

<span data-ttu-id="cbf73-122">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="cbf73-122">Record the following information:</span></span>

* <span data-ttu-id="cbf73-123">*Application API serveur* ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="cbf73-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="cbf73-124">ID de répertoire (locataire) (par exemple, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="cbf73-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="cbf73-125">Le domaine principal/serveur de publication/client AAD (par exemple, `contoso.onmicrosoft.com` ) : le domaine est disponible en tant que domaine du serveur de **publication** dans le panneau de **personnalisation** du portail Azure pour l’application inscrite.</span><span class="sxs-lookup"><span data-stu-id="cbf73-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="cbf73-126">Dans **autorisations d’API**, supprimez l’autorisation **Microsoft Graph**  >  **User. Read** , car l’application ne nécessite pas d’accès de connexion ou de profil utilisateur.</span><span class="sxs-lookup"><span data-stu-id="cbf73-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="cbf73-127">Dans **exposer une API**:</span><span class="sxs-lookup"><span data-stu-id="cbf73-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="cbf73-128">sélectionner **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="cbf73-129">Sélectionnez **Enregistrer et continuer**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="cbf73-130">Spécifiez un **nom d’étendue** (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="cbf73-131">Spécifiez un **nom d’affichage du consentement administrateur** (par exemple, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="cbf73-132">Fournissez une **Description du consentement** de l’administrateur (par exemple, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="cbf73-133">Confirmez que l' **État** est défini sur **activé**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="cbf73-134">Sélectionnez **Ajouter une étendue**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-134">Select **Add scope**.</span></span>

<span data-ttu-id="cbf73-135">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="cbf73-135">Record the following information:</span></span>

* <span data-ttu-id="cbf73-136">URI ID d’application (par exemple,, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ou la valeur personnalisée que vous fournissez)</span><span class="sxs-lookup"><span data-stu-id="cbf73-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="cbf73-137">Nom de l’étendue (par exemple, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="cbf73-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="cbf73-138">Inscrire une application cliente</span><span class="sxs-lookup"><span data-stu-id="cbf73-138">Register a client app</span></span>

<span data-ttu-id="cbf73-139">Inscrire une application AAD pour l' *application cliente*:</span><span class="sxs-lookup"><span data-stu-id="cbf73-139">Register an AAD app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="cbf73-140">Dans **Azure Active Directory** > **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="cbf73-141">Fournissez un **nom** pour l’application (par exemple, **Blazor client AAD**).</span><span class="sxs-lookup"><span data-stu-id="cbf73-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="cbf73-142">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="cbf73-143">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="cbf73-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="cbf73-144">Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="cbf73-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="cbf73-145">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="cbf73-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="cbf73-146">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="cbf73-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="cbf73-147">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les *`Server`* Propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="cbf73-148">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="cbf73-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="cbf73-149">Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="cbf73-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="cbf73-150">Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="cbf73-151">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-151">Select **Register**.</span></span>

<span data-ttu-id="cbf73-152">Enregistrez l' *`Client`* ID de l’application (client) de l’application (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="cbf73-153">Dans configurations de plateforme **d’authentification** , >  > **application à page unique (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="cbf73-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="cbf73-154">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="cbf73-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="cbf73-155">Pour **octroi implicite**, assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="cbf73-155">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="cbf73-156">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="cbf73-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="cbf73-157">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="cbf73-158">Dans **Azure Active Directory** > **inscriptions d’applications**, sélectionnez **nouvelle inscription**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-158">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="cbf73-159">Fournissez un **nom** pour l’application (par exemple, **Blazor client AAD**).</span><span class="sxs-lookup"><span data-stu-id="cbf73-159">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="cbf73-160">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-160">Choose a **Supported account types**.</span></span> <span data-ttu-id="cbf73-161">Pour cette expérience, vous pouvez sélectionner des **comptes dans ce répertoire d’organisation uniquement** (un seul locataire).</span><span class="sxs-lookup"><span data-stu-id="cbf73-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="cbf73-162">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="cbf73-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="cbf73-163">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="cbf73-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="cbf73-164">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="cbf73-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="cbf73-165">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les *`Server`* Propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="cbf73-166">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="cbf73-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="cbf73-167">Une remarque apparaît dans la section [créer une application](#create-the-app) pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="cbf73-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="cbf73-168">Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="cbf73-169">Sélectionnez **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-169">Select **Register**.</span></span>

<span data-ttu-id="cbf73-170">Enregistrez l' *`Client`* ID de l’application (client) de l’application (par exemple, `4369008b-21fa-427c-abaa-9b53bf58e538` ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="cbf73-171">Dans  le >  > **site Web** configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="cbf73-171">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="cbf73-172">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="cbf73-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="cbf73-173">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-173">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="cbf73-174">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="cbf73-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="cbf73-175">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="cbf73-176">Dans **autorisations d’API**:</span><span class="sxs-lookup"><span data-stu-id="cbf73-176">In **API permissions**:</span></span>

1. <span data-ttu-id="cbf73-177">Vérifiez que l’application a **Microsoft Graph**  >  autorisation **User. Read** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="cbf73-178">Sélectionnez **Ajouter une autorisation** suivi de **mes API**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-178">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="cbf73-179">Sélectionnez l' *application API serveur* dans la colonne **nom** (par exemple, **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="cbf73-179">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="cbf73-180">Ouvrez la liste des **API** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-180">Open the **API** list.</span></span>
1. <span data-ttu-id="cbf73-181">Activez l’accès à l’API (par exemple, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="cbf73-182">Sélectionnez **Ajouter des autorisations**.</span><span class="sxs-lookup"><span data-stu-id="cbf73-182">Select **Add permissions**.</span></span>
1. <span data-ttu-id="cbf73-183">Sélectionnez le bouton **accorder le consentement de l’administrateur pour {nom du locataire}** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="cbf73-184">Sélectionnez **Oui** pour confirmer.</span><span class="sxs-lookup"><span data-stu-id="cbf73-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="cbf73-185">Créer l'application</span><span class="sxs-lookup"><span data-stu-id="cbf73-185">Create the app</span></span>

<span data-ttu-id="cbf73-186">Dans un dossier vide, remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="cbf73-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="cbf73-187">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="cbf73-187">Placeholder</span></span>                  | <span data-ttu-id="cbf73-188">Nom du portail Azure</span><span class="sxs-lookup"><span data-stu-id="cbf73-188">Azure portal name</span></span>                                     | <span data-ttu-id="cbf73-189">Exemple</span><span class="sxs-lookup"><span data-stu-id="cbf73-189">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="cbf73-190">ID de l’application (client) pour l' *`Client`* application</span><span class="sxs-lookup"><span data-stu-id="cbf73-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="cbf73-191">Nom de l’étendue</span><span class="sxs-lookup"><span data-stu-id="cbf73-191">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="cbf73-192">ID de l’application (client) pour l’application *API serveur*</span><span class="sxs-lookup"><span data-stu-id="cbf73-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="cbf73-193">URI d’ID d’application&dagger;</span><span class="sxs-lookup"><span data-stu-id="cbf73-193">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="cbf73-194">Domaine principal/serveur de publication/locataire</span><span class="sxs-lookup"><span data-stu-id="cbf73-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="cbf73-195">ID de l’annuaire (locataire)</span><span class="sxs-lookup"><span data-stu-id="cbf73-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="cbf73-196">&dagger;Le Blazor WebAssembly modèle ajoute automatiquement un schéma de `api://` à l’argument d’URI ID d’application passé dans la `dotnet new` commande.</span><span class="sxs-lookup"><span data-stu-id="cbf73-196">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="cbf73-197">Lorsque vous fournissez l’URI ID d’application pour l' `{SERVER API APP ID URI}` espace réservé et si le schéma est `api://` , supprimez le schéma ( `api://` ) de l’argument, comme le montre l’exemple de valeur dans le tableau précédent.</span><span class="sxs-lookup"><span data-stu-id="cbf73-197">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="cbf73-198">Si l’URI ID d’application est une valeur personnalisée ou s’il en existe un autre (par exemple, `https://` pour un domaine du serveur de publication non approuvé similaire à `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), vous devez mettre à jour manuellement l’URI de l’étendue par défaut et supprimer le `api://` schéma une fois que l' *`Client`* application a été créée par le modèle.</span><span class="sxs-lookup"><span data-stu-id="cbf73-198">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="cbf73-199">Pour plus d’informations, consultez la remarque dans la section [étendues de jetons d’accès](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="cbf73-199">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="cbf73-200">Le Blazor WebAssembly modèle peut être modifié dans une version ultérieure de ASP.net Core pour résoudre ces scénarios.</span><span class="sxs-lookup"><span data-stu-id="cbf73-200">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="cbf73-201">Pour plus d’informations, consultez [schéma double pour l’URI ID d’application avec Blazor modèle WASM (hébergé, unique org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="cbf73-201">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="cbf73-202">L’emplacement de sortie spécifié avec l’option `-o|--output` crée un dossier de projet s’il n’existe pas et devient partie intégrante du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="cbf73-202">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="cbf73-203">Une modification de configuration peut être nécessaire lors de l’utilisation d’un client Azure avec un domaine d’éditeur non vérifié, qui est décrit dans la section paramètres de l' [application](#app-settings) .</span><span class="sxs-lookup"><span data-stu-id="cbf73-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="cbf73-204">Une modification de configuration peut être nécessaire lors de l’utilisation d’un client Azure avec un domaine d’éditeur non vérifié, qui est décrit dans la section [étendues de jeton d’accès](#access-token-scopes) .</span><span class="sxs-lookup"><span data-stu-id="cbf73-204">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="cbf73-205">Dans le Portail Azure, l' *`Client`* **URI de redirection** de la configuration de plateforme de l’application est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="cbf73-205">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="cbf73-206">Si l' *`Client`* application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l' *application API serveur* dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-206">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="cbf73-207">Si le port n’a pas été configuré précédemment avec le *`Client`* port connu de l’application, revenez à l’inscription de l' *`Client`* application dans la portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="cbf73-207">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="cbf73-208">*`Server`* configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="cbf73-208">*`Server`* app configuration</span></span>

<span data-ttu-id="cbf73-209">*Cette section se rapporte à l’application de la solution **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="cbf73-209">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="cbf73-210">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="cbf73-210">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cbf73-211">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core les API Web avec la Identity plate-forme Microsoft est assurée par les packages suivants :</span><span class="sxs-lookup"><span data-stu-id="cbf73-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="cbf73-212">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur NuGet.org.</span><span class="sxs-lookup"><span data-stu-id="cbf73-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="cbf73-213">La prise en charge de l’authentification et de l’autorisation des appels à ASP.NET Core API Web est assurée par le [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) Package :</span><span class="sxs-lookup"><span data-stu-id="cbf73-213">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="cbf73-214">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span><span class="sxs-lookup"><span data-stu-id="cbf73-214">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="cbf73-215">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="cbf73-215">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cbf73-216">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="cbf73-216">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="cbf73-217">La <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> méthode Configure les services pour protéger l’API Web avec Microsoft Identity Platform v 2.0.</span><span class="sxs-lookup"><span data-stu-id="cbf73-217">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="cbf73-218">Cette méthode attend une `AzureAd` section dans la configuration de l’application avec les paramètres nécessaires pour initialiser les options d’authentification.</span><span class="sxs-lookup"><span data-stu-id="cbf73-218">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="cbf73-219">La `AddAuthentication` méthode définit les services d’authentification dans l’application et configure le gestionnaire du porteur JWT comme méthode d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="cbf73-219">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="cbf73-220">La <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> méthode définit les paramètres spécifiques dans le gestionnaire du porteur JWT requis pour valider les jetons émis par l’Azure Active Directory :</span><span class="sxs-lookup"><span data-stu-id="cbf73-220">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="cbf73-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> et <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> Assurez-vous que :</span><span class="sxs-lookup"><span data-stu-id="cbf73-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="cbf73-222">L’application tente d’analyser et de valider les jetons sur les demandes entrantes.</span><span class="sxs-lookup"><span data-stu-id="cbf73-222">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="cbf73-223">Toute demande d’accès à une ressource protégée sans informations d’identification appropriées échoue.</span><span class="sxs-lookup"><span data-stu-id="cbf73-223">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="cbf73-224">Utilisateur. Identity . Nomme</span><span class="sxs-lookup"><span data-stu-id="cbf73-224">User.Identity.Name</span></span>

<span data-ttu-id="cbf73-225">Par défaut, l' *`Server`* API d’application remplit `User.Identity.Name` la valeur du type de `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` revendication (par exemple, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-225">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="cbf73-226">Pour configurer l’application afin qu’elle reçoive la valeur du `name` type de revendication :</span><span class="sxs-lookup"><span data-stu-id="cbf73-226">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="cbf73-227">Ajoutez un espace de noms pour <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> à `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="cbf73-227">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="cbf73-228">Configurez le <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cbf73-228">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="cbf73-229">Configurez le <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> du <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="cbf73-229">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="cbf73-230">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="cbf73-230">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cbf73-231">Le `appsettings.json` fichier contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès :</span><span class="sxs-lookup"><span data-stu-id="cbf73-231">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="cbf73-232">Exemple :</span><span class="sxs-lookup"><span data-stu-id="cbf73-232">Example:</span></span>

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

[!INCLUDE[](~/blazor/includes/security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="cbf73-233">Le `appsettings.json` fichier contient les options permettant de configurer le gestionnaire du porteur JWT utilisé pour valider les jetons d’accès :</span><span class="sxs-lookup"><span data-stu-id="cbf73-233">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="cbf73-234">Exemple :</span><span class="sxs-lookup"><span data-stu-id="cbf73-234">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="cbf73-235">Contrôleur WeatherForecast</span><span class="sxs-lookup"><span data-stu-id="cbf73-235">WeatherForecast controller</span></span>

<span data-ttu-id="cbf73-236">Le contrôleur WeatherForecast (*Controllers/WeatherForecastController. cs*) expose une API protégée avec l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut appliqué au contrôleur.</span><span class="sxs-lookup"><span data-stu-id="cbf73-236">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="cbf73-237">Il est **important** de comprendre que :</span><span class="sxs-lookup"><span data-stu-id="cbf73-237">It's **important** to understand that:</span></span>

* <span data-ttu-id="cbf73-238">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut dans ce contrôleur d’API est la seule chose qui protège cette API contre tout accès non autorisé.</span><span class="sxs-lookup"><span data-stu-id="cbf73-238">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="cbf73-239">L' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut utilisé dans l' Blazor WebAssembly application sert uniquement d’indicateur à l’application que l’utilisateur doit être autorisé à utiliser correctement pour l’application.</span><span class="sxs-lookup"><span data-stu-id="cbf73-239">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="cbf73-240">*`Client`* configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="cbf73-240">*`Client`* app configuration</span></span>

<span data-ttu-id="cbf73-241">*Cette section se rapporte à l’application de la solution **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="cbf73-241">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="cbf73-242">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="cbf73-242">Authentication package</span></span>

<span data-ttu-id="cbf73-243">Quand une application est créée pour utiliser des comptes professionnels ou scolaires ( `SingleOrg` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-243">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="cbf73-244">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="cbf73-244">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="cbf73-245">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="cbf73-245">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="cbf73-246">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="cbf73-246">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="cbf73-247">Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.</span><span class="sxs-lookup"><span data-stu-id="cbf73-247">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="cbf73-248">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="cbf73-248">Authentication service support</span></span>

<span data-ttu-id="cbf73-249">La prise en charge des <xref:System.Net.Http.HttpClient> instances de qui incluent des jetons d’accès lors de l’exécution de demandes vers le projet serveur est ajoutée.</span><span class="sxs-lookup"><span data-stu-id="cbf73-249">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="cbf73-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="cbf73-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="cbf73-251">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-251">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="cbf73-252">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package.</span><span class="sxs-lookup"><span data-stu-id="cbf73-252">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="cbf73-253">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="cbf73-253">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="cbf73-254">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="cbf73-254">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="cbf73-255">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="cbf73-255">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="cbf73-256">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD du portail Azure lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="cbf73-256">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="cbf73-257">La configuration est fournie par le `wwwroot/appsettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="cbf73-257">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="cbf73-258">Exemple :</span><span class="sxs-lookup"><span data-stu-id="cbf73-258">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="cbf73-259">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="cbf73-259">Access token scopes</span></span>

<span data-ttu-id="cbf73-260">Les étendues de jeton d’accès par défaut représentent la liste des étendues de jeton d’accès qui sont :</span><span class="sxs-lookup"><span data-stu-id="cbf73-260">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="cbf73-261">Inclus par défaut dans la demande de connexion.</span><span class="sxs-lookup"><span data-stu-id="cbf73-261">Included by default in the sign in request.</span></span>
* <span data-ttu-id="cbf73-262">Utilisé pour approvisionner un jeton d’accès immédiatement après l’authentification.</span><span class="sxs-lookup"><span data-stu-id="cbf73-262">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="cbf73-263">Toutes les étendues doivent appartenir à la même application par Azure Active Directory règles.</span><span class="sxs-lookup"><span data-stu-id="cbf73-263">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="cbf73-264">Des étendues supplémentaires peuvent être ajoutées pour d’autres applications API en fonction des besoins :</span><span class="sxs-lookup"><span data-stu-id="cbf73-264">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="cbf73-265">Le Blazor WebAssembly modèle ajoute automatiquement un schéma de `api://` à l’argument d’URI ID d’application passé dans la `dotnet new` commande.</span><span class="sxs-lookup"><span data-stu-id="cbf73-265">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="cbf73-266">Lors de la génération d’une application à partir du Blazor modèle de projet, vérifiez que la valeur de l’étendue du jeton d’accès par défaut utilise la valeur d’URI ID d’application personnalisée correcte que vous avez fournie dans le portail Azure ou une valeur avec l' **un** des formats suivants :</span><span class="sxs-lookup"><span data-stu-id="cbf73-266">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="cbf73-267">Lorsque le domaine de l’éditeur de l’annuaire est **approuvé**, l’étendue du jeton d’accès par défaut est généralement une valeur similaire à celle de l’exemple suivant, où `API.Access` est le nom de l’étendue par défaut :</span><span class="sxs-lookup"><span data-stu-id="cbf73-267">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="cbf73-268">Inspectez la valeur d’un schéma double ( `api://api://...` ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-268">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="cbf73-269">Si un double schéma est présent, supprimez le premier `api://` schéma de la valeur.</span><span class="sxs-lookup"><span data-stu-id="cbf73-269">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="cbf73-270">Lorsque le domaine du serveur de publication de l’annuaire n’est pas **approuvé**, l’étendue du jeton d’accès par défaut est généralement une valeur similaire à celle de l’exemple suivant, où `API.Access` est le nom de l’étendue par défaut :</span><span class="sxs-lookup"><span data-stu-id="cbf73-270">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="cbf73-271">Inspectez la valeur d’un `api://` schéma supplémentaire ( `api://https://contoso.onmicrosoft.com/...` ).</span><span class="sxs-lookup"><span data-stu-id="cbf73-271">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="cbf73-272">Si un `api://` schéma supplémentaire est présent, supprimez le `api://` schéma de la valeur.</span><span class="sxs-lookup"><span data-stu-id="cbf73-272">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="cbf73-273">Le Blazor WebAssembly modèle peut être modifié dans une version ultérieure de ASP.net Core pour résoudre ces scénarios.</span><span class="sxs-lookup"><span data-stu-id="cbf73-273">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="cbf73-274">Pour plus d’informations, consultez [schéma double pour l’URI ID d’application avec Blazor modèle WASM (hébergé, unique org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="cbf73-274">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="cbf73-275">Spécifier des étendues supplémentaires avec `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="cbf73-275">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="cbf73-276">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="cbf73-276">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="cbf73-277">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cbf73-277">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="cbf73-278">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="cbf73-278">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="cbf73-279">Mode de connexion</span><span class="sxs-lookup"><span data-stu-id="cbf73-279">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="cbf73-280">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="cbf73-280">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="cbf73-281">Page d'index</span><span class="sxs-lookup"><span data-stu-id="cbf73-281">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="cbf73-282">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="cbf73-282">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="cbf73-283">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="cbf73-283">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="cbf73-284">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="cbf73-284">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="cbf73-285">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="cbf73-285">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="cbf73-286">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="cbf73-286">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="cbf73-287">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="cbf73-287">Run the app</span></span>

<span data-ttu-id="cbf73-288">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="cbf73-288">Run the app from the Server project.</span></span> <span data-ttu-id="cbf73-289">Lorsque vous utilisez Visual Studio, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="cbf73-289">When using Visual Studio, either:</span></span>

* <span data-ttu-id="cbf73-290">Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-290">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="cbf73-291">Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="cbf73-291">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="cbf73-292">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="cbf73-292">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="cbf73-293">Créer une version personnalisée de la bibliothèque JavaScript Authentication. MSAL</span><span class="sxs-lookup"><span data-stu-id="cbf73-293">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="cbf73-294">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="cbf73-294">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="cbf73-295">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="cbf73-295">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
* [<span data-ttu-id="cbf73-296">Démarrage rapide : Inscrire une application à l’aide de la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="cbf73-296">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app)
