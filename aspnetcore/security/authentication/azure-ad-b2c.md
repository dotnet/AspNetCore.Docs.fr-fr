---
title: Authentification Cloud avec Azure Active Directory B2C dans ASP.NET Core
author: camsoper
description: Découvrez comment configurer l’authentification Azure Active Directory B2C avec ASP.NET Core.
ms.author: casoper
ms.custom: devx-track-csharp, mvc
ms.date: 01/21/2019
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
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: f917bec8f2d929e62bf43494159a63458f135c5f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061390"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="45a30-103">Authentification Cloud avec Azure Active Directory B2C dans ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45a30-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="45a30-104">Auteur : [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="45a30-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="45a30-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure ad B2C) est une solution de gestion des identités dans le Cloud pour les applications Web et mobiles.</span><span class="sxs-lookup"><span data-stu-id="45a30-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="45a30-106">Le service fournit une authentification pour les applications hébergées dans le Cloud et localement.</span><span class="sxs-lookup"><span data-stu-id="45a30-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="45a30-107">Les types d’authentification incluent les comptes individuels, les comptes de réseaux sociaux et les comptes d’entreprise fédérés.</span><span class="sxs-lookup"><span data-stu-id="45a30-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="45a30-108">En outre, Azure AD B2C pouvez fournir une authentification multifacteur avec une configuration minimale.</span><span class="sxs-lookup"><span data-stu-id="45a30-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="45a30-109">Azure Active Directory (Azure AD) et Azure AD B2C sont des offres de produits distinctes.</span><span class="sxs-lookup"><span data-stu-id="45a30-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="45a30-110">Un locataire Azure AD représente une organisation, tandis qu’un locataire Azure AD B2C représente une collection d’identités à utiliser avec des applications par partie de confiance.</span><span class="sxs-lookup"><span data-stu-id="45a30-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="45a30-111">Pour plus d’informations, consultez [Azure ad B2C : Forum aux questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="45a30-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="45a30-112">Dans ce tutoriel, vous allez apprendre à effectuer les actions suivantes :</span><span class="sxs-lookup"><span data-stu-id="45a30-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="45a30-113">Créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="45a30-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="45a30-114">Inscrire une application dans Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="45a30-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="45a30-115">Utiliser Visual Studio pour créer une application Web ASP.NET Core configurée pour utiliser le locataire Azure AD B2C pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="45a30-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="45a30-116">Configurer des stratégies contrôlant le comportement du locataire Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="45a30-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="45a30-117">Prérequis</span><span class="sxs-lookup"><span data-stu-id="45a30-117">Prerequisites</span></span>

<span data-ttu-id="45a30-118">Les éléments suivants sont requis pour cette procédure pas à pas :</span><span class="sxs-lookup"><span data-stu-id="45a30-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="45a30-119">Abonnement Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="45a30-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="45a30-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="45a30-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="45a30-121">Créer le locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="45a30-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="45a30-122">Créez un locataire Azure Active Directory B2C [comme décrit dans la documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="45a30-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="45a30-123">Lorsque vous y êtes invité, l’Association du locataire à un abonnement Azure est facultative pour ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="45a30-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="45a30-124">Inscrire l’application dans Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="45a30-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="45a30-125">Dans le locataire nouvellement créé Azure AD B2C, inscrivez votre application en suivant [les étapes décrites dans la documentation de](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) la section **inscrire une application Web** .</span><span class="sxs-lookup"><span data-stu-id="45a30-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="45a30-126">Arrêtez dans la section **créer une clé secrète client d’application Web** .</span><span class="sxs-lookup"><span data-stu-id="45a30-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="45a30-127">Une clé secrète client n’est pas nécessaire pour ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="45a30-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="45a30-128">Utilisez les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="45a30-128">Use the following values:</span></span>

| <span data-ttu-id="45a30-129">Paramètre</span><span class="sxs-lookup"><span data-stu-id="45a30-129">Setting</span></span>                       | <span data-ttu-id="45a30-130">Valeur</span><span class="sxs-lookup"><span data-stu-id="45a30-130">Value</span></span>                     | <span data-ttu-id="45a30-131">Notes</span><span class="sxs-lookup"><span data-stu-id="45a30-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="45a30-132">**Nom**</span><span class="sxs-lookup"><span data-stu-id="45a30-132">**Name**</span></span>                      | <span data-ttu-id="45a30-133">*&lt;nom de l’application&gt;*</span><span class="sxs-lookup"><span data-stu-id="45a30-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="45a30-134">Entrez un **nom** pour l’application qui décrit votre application aux consommateurs.</span><span class="sxs-lookup"><span data-stu-id="45a30-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="45a30-135">**Inclure une application/API web**</span><span class="sxs-lookup"><span data-stu-id="45a30-135">**Include web app / web API**</span></span> | <span data-ttu-id="45a30-136">Yes</span><span class="sxs-lookup"><span data-stu-id="45a30-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="45a30-137">**Autoriser le flux implicite**</span><span class="sxs-lookup"><span data-stu-id="45a30-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="45a30-138">Yes</span><span class="sxs-lookup"><span data-stu-id="45a30-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="45a30-139">**URL de réponse**</span><span class="sxs-lookup"><span data-stu-id="45a30-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="45a30-140">Les URL de réponse sont des points de terminaison auxquels Azure AD B2C retourne les jetons demandés par votre application.</span><span class="sxs-lookup"><span data-stu-id="45a30-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="45a30-141">Visual Studio fournit l’URL de réponse à utiliser.</span><span class="sxs-lookup"><span data-stu-id="45a30-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="45a30-142">Pour le moment, entrez `https://localhost:44300/signin-oidc` pour remplir le formulaire.</span><span class="sxs-lookup"><span data-stu-id="45a30-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="45a30-143">**URI ID de l’application**</span><span class="sxs-lookup"><span data-stu-id="45a30-143">**App ID URI**</span></span>                | <span data-ttu-id="45a30-144">Laisser vide</span><span class="sxs-lookup"><span data-stu-id="45a30-144">Leave blank</span></span>               | <span data-ttu-id="45a30-145">Non requis pour ce didacticiel.</span><span class="sxs-lookup"><span data-stu-id="45a30-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="45a30-146">**Inclure le client natif**</span><span class="sxs-lookup"><span data-stu-id="45a30-146">**Include native client**</span></span>     | <span data-ttu-id="45a30-147">No</span><span class="sxs-lookup"><span data-stu-id="45a30-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="45a30-148">Si vous configurez une URL de réponse non-localhost, tenez compte des [contraintes sur ce qui est autorisé dans la liste des URL de réponse](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span><span class="sxs-lookup"><span data-stu-id="45a30-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="45a30-149">Une fois l’application inscrite, la liste des applications du locataire s’affiche.</span><span class="sxs-lookup"><span data-stu-id="45a30-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="45a30-150">Sélectionnez l’application qui vient d’être inscrite.</span><span class="sxs-lookup"><span data-stu-id="45a30-150">Select the app that was just registered.</span></span> <span data-ttu-id="45a30-151">Sélectionnez l’icône de **copie** à droite du champ **ID** de l’application pour la copier dans le presse-papiers.</span><span class="sxs-lookup"><span data-stu-id="45a30-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="45a30-152">Rien ne peut être configuré dans le locataire Azure AD B2C pour l’instant, mais laissez la fenêtre du navigateur ouverte.</span><span class="sxs-lookup"><span data-stu-id="45a30-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="45a30-153">Une fois l’application ASP.NET Core créée, il y a plus de configuration.</span><span class="sxs-lookup"><span data-stu-id="45a30-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="45a30-154">Créer une application ASP.NET Core dans Visual Studio</span><span class="sxs-lookup"><span data-stu-id="45a30-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="45a30-155">Le modèle d’application Web Visual Studio peut être configuré pour utiliser le locataire Azure AD B2C pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="45a30-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="45a30-156">Dans Visual Studio :</span><span class="sxs-lookup"><span data-stu-id="45a30-156">In Visual Studio:</span></span>

1. <span data-ttu-id="45a30-157">Créez une application web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="45a30-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="45a30-158">Dans la liste des modèles, sélectionnez **application Web** .</span><span class="sxs-lookup"><span data-stu-id="45a30-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="45a30-159">Sélectionnez le bouton **modifier l’authentification** .</span><span class="sxs-lookup"><span data-stu-id="45a30-159">Select the **Change Authentication** button.</span></span>
    
    ![Bouton modifier l’authentification](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="45a30-161">Dans la boîte de dialogue **modifier l’authentification** , sélectionnez **comptes d’utilisateur individuels** , puis sélectionnez **se connecter à un magasin d’utilisateurs existant dans le Cloud** dans la liste déroulante.</span><span class="sxs-lookup"><span data-stu-id="45a30-161">In the **Change Authentication** dialog, select **Individual User Accounts** , and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Boîte de dialogue Modifier l’authentification](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="45a30-163">Remplissez le formulaire avec les valeurs suivantes :</span><span class="sxs-lookup"><span data-stu-id="45a30-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="45a30-164">Paramètre</span><span class="sxs-lookup"><span data-stu-id="45a30-164">Setting</span></span>                       | <span data-ttu-id="45a30-165">Valeur</span><span class="sxs-lookup"><span data-stu-id="45a30-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="45a30-166">**Nom de domaine**</span><span class="sxs-lookup"><span data-stu-id="45a30-166">**Domain Name**</span></span>               | <span data-ttu-id="45a30-167">*&lt;nom de domaine de votre client B2C&gt;*</span><span class="sxs-lookup"><span data-stu-id="45a30-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="45a30-168">**ID de l’application**</span><span class="sxs-lookup"><span data-stu-id="45a30-168">**Application ID**</span></span>            | <span data-ttu-id="45a30-169">*&lt;coller l’ID d’application à partir du presse-papiers&gt;*</span><span class="sxs-lookup"><span data-stu-id="45a30-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="45a30-170">**Chemin de rappel**</span><span class="sxs-lookup"><span data-stu-id="45a30-170">**Callback Path**</span></span>             | <span data-ttu-id="45a30-171">*&lt;utiliser la valeur par défaut&gt;*</span><span class="sxs-lookup"><span data-stu-id="45a30-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="45a30-172">**Stratégie d’inscription ou de connexion**</span><span class="sxs-lookup"><span data-stu-id="45a30-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="45a30-173">**Réinitialiser la stratégie de mot de passe**</span><span class="sxs-lookup"><span data-stu-id="45a30-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="45a30-174">**Modifier la stratégie de profil**</span><span class="sxs-lookup"><span data-stu-id="45a30-174">**Edit profile policy**</span></span>       | <span data-ttu-id="45a30-175">*&lt;laisser vide&gt;*</span><span class="sxs-lookup"><span data-stu-id="45a30-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="45a30-176">Sélectionnez le lien **copier** en regard de **URI de réponse** pour copier l’URI de réponse dans le presse-papiers.</span><span class="sxs-lookup"><span data-stu-id="45a30-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="45a30-177">Sélectionnez **OK** pour fermer la boîte de dialogue **modifier l’authentification** .</span><span class="sxs-lookup"><span data-stu-id="45a30-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="45a30-178">Sélectionnez **OK** pour créer l’application Web.</span><span class="sxs-lookup"><span data-stu-id="45a30-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="45a30-179">Terminer l’inscription de l’application B2C</span><span class="sxs-lookup"><span data-stu-id="45a30-179">Finish the B2C app registration</span></span>

<span data-ttu-id="45a30-180">Revenez à la fenêtre du navigateur avec les propriétés de l’application B2C toujours ouverte.</span><span class="sxs-lookup"><span data-stu-id="45a30-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="45a30-181">Modifiez l' **URL de réponse** temporaire spécifiée précédemment pour la valeur copiée à partir de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="45a30-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="45a30-182">Sélectionnez **Enregistrer** en haut de la fenêtre.</span><span class="sxs-lookup"><span data-stu-id="45a30-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="45a30-183">Si vous n’avez pas copié l’URL de réponse, utilisez l’adresse HTTPs à partir de l’onglet Déboguer dans les propriétés du projet Web, puis ajoutez la valeur **CallbackPath** à partir de *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="45a30-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *:::no-loc(appsettings.json):::* .</span></span>

## <a name="configure-policies"></a><span data-ttu-id="45a30-184">Configurer des stratégies</span><span class="sxs-lookup"><span data-stu-id="45a30-184">Configure policies</span></span>

<span data-ttu-id="45a30-185">Utilisez les étapes de la documentation de Azure AD B2C pour [créer une stratégie d’inscription ou de connexion](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), puis [créez une stratégie de réinitialisation de mot de passe](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="45a30-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="45a30-186">Utilisez les exemples de valeurs fournis dans la documentation pour les **:::no-loc(Identity)::: fournisseurs** , les **attributs d’inscription** et les **revendications d’application** .</span><span class="sxs-lookup"><span data-stu-id="45a30-186">Use the example values provided in the documentation for **:::no-loc(Identity)::: providers** , **Sign-up attributes** , and **Application claims** .</span></span> <span data-ttu-id="45a30-187">L’utilisation du bouton **Exécuter maintenant** pour tester les stratégies, comme décrit dans la documentation, est facultative.</span><span class="sxs-lookup"><span data-stu-id="45a30-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="45a30-188">Vérifiez que les noms de stratégie sont exactement comme décrit dans la documentation, car ces stratégies ont été utilisées dans la boîte de dialogue **modifier l’authentification** dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="45a30-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="45a30-189">Les noms de stratégie peuvent être vérifiés dans *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="45a30-189">The policy names can be verified in *:::no-loc(appsettings.json):::* .</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a><span data-ttu-id="45a30-190">Configurer les options OpenIdConnectOptions/JwtBearer/sous-jacentes :::no-loc(Cookie):::</span><span class="sxs-lookup"><span data-stu-id="45a30-190">Configure the underlying OpenIdConnectOptions/JwtBearer/:::no-loc(Cookie)::: options</span></span>

<span data-ttu-id="45a30-191">Pour configurer directement les options sous-jacentes, utilisez la constante de schéma appropriée dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="45a30-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<:::no-loc(Cookie):::AuthenticationOptions>(
    AzureAD[B2C]Defaults.:::no-loc(Cookie):::Scheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="45a30-192">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="45a30-192">Run the app</span></span>

<span data-ttu-id="45a30-193">Dans Visual Studio, appuyez sur **F5** pour générer et exécuter l’application.</span><span class="sxs-lookup"><span data-stu-id="45a30-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="45a30-194">Après le lancement de l’application Web, sélectionnez **accepter** pour accepter l’utilisation des :::no-loc(cookie)::: s (si vous y êtes invité), puis sélectionnez **se connecter** .</span><span class="sxs-lookup"><span data-stu-id="45a30-194">After the web app launches, select **Accept** to accept the use of :::no-loc(cookie):::s (if prompted), and then select **Sign in** .</span></span>

![Connectez-vous à l’application](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="45a30-196">Le navigateur redirige vers le locataire Azure AD B2C.</span><span class="sxs-lookup"><span data-stu-id="45a30-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="45a30-197">Connectez-vous avec un compte existant (s’il a été créé en testant les stratégies) ou sélectionnez **Inscrivez-vous maintenant** pour créer un nouveau compte.</span><span class="sxs-lookup"><span data-stu-id="45a30-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="45a30-198">Le lien **vous avez oublié votre mot de passe ?** est utilisé pour réinitialiser un mot de passe oublié.</span><span class="sxs-lookup"><span data-stu-id="45a30-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Connexion à Azure AD B2C](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="45a30-200">Une fois la connexion établie, le navigateur redirige vers l’application Web.</span><span class="sxs-lookup"><span data-stu-id="45a30-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Succès](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="45a30-202">Étapes suivantes</span><span class="sxs-lookup"><span data-stu-id="45a30-202">Next steps</span></span>

<span data-ttu-id="45a30-203">Dans ce didacticiel, vous avez appris à :</span><span class="sxs-lookup"><span data-stu-id="45a30-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="45a30-204">Créer un locataire Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="45a30-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="45a30-205">Inscrire une application dans Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="45a30-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="45a30-206">Utiliser Visual Studio pour créer une application Web ASP.NET Core configurée pour utiliser le locataire Azure AD B2C pour l’authentification</span><span class="sxs-lookup"><span data-stu-id="45a30-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="45a30-207">Configurer des stratégies contrôlant le comportement du locataire Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="45a30-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="45a30-208">Maintenant que l’application ASP.NET Core est configurée pour utiliser Azure AD B2C pour l’authentification, vous pouvez utiliser l' [attribut Authorize](xref:security/authorization/simple) pour sécuriser votre application.</span><span class="sxs-lookup"><span data-stu-id="45a30-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="45a30-209">Poursuivez le développement de votre application en apprenant à :</span><span class="sxs-lookup"><span data-stu-id="45a30-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="45a30-210">[Personnaliser l’interface utilisateur Azure ad B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="45a30-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="45a30-211">[Configurer les exigences de complexité du mot de passe](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span><span class="sxs-lookup"><span data-stu-id="45a30-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="45a30-212">[Activez Multi-Factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="45a30-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="45a30-213">Configurez des fournisseurs d’identité supplémentaires, tels que [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), etc.</span><span class="sxs-lookup"><span data-stu-id="45a30-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="45a30-214">[Utilisez le API Graph Azure ad](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) pour récupérer des informations utilisateur supplémentaires, telles que l’appartenance à un groupe, à partir du locataire Azure ad B2C.</span><span class="sxs-lookup"><span data-stu-id="45a30-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="45a30-215">[Comment sécuriser une API Web générée avec ASP.net core à l’aide de l’Azure ad B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span><span class="sxs-lookup"><span data-stu-id="45a30-215">[How to secure a Web API built with ASP.NET Core using the Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span></span>
* <span data-ttu-id="45a30-216">[Didacticiel : accorder l’accès à une API web ASP.net à l’aide de Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="45a30-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>
