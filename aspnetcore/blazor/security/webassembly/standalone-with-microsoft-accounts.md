---
title: Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec des comptes Microsoft
author: guardrex
description: Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core autonome avec des comptes Microsoft.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 49f15e3e0d68ec475bb4c9a047d6c5e37d314a42
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854455"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="749f2-103">Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec des comptes Microsoft</span><span class="sxs-lookup"><span data-stu-id="749f2-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="749f2-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="749f2-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="749f2-105">Cet article explique comment créer une [ Blazor WebAssembly application autonome](xref:blazor/hosting-models#blazor-webassembly) qui utilise [des comptes Microsoft avec Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="749f2-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication.</span></span>

<span data-ttu-id="749f2-106">Inscrire une application AAD dans la   >  zone de **inscriptions d’applications** Azure Active Directory de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="749f2-106">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="749f2-107">Fournissez un **nom** pour l’application (par exemple, **Blazor comptes Microsoft AAD autonomes**).</span><span class="sxs-lookup"><span data-stu-id="749f2-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="749f2-108">Dans **types de comptes pris en charge**, sélectionnez **comptes dans n’importe quel annuaire d’organisation**.</span><span class="sxs-lookup"><span data-stu-id="749f2-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="749f2-109">Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="749f2-109">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="749f2-110">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="749f2-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="749f2-111">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="749f2-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="749f2-112">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="749f2-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="749f2-113">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="749f2-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="749f2-114">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="749f2-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="749f2-115">Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="749f2-115">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="749f2-116">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="749f2-116">Select **Register**.</span></span>

<span data-ttu-id="749f2-117">Enregistrez l’ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="749f2-117">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="749f2-118">Dans configurations de plateforme **d’authentification** , >  > **application à page unique (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="749f2-118">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="749f2-119">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="749f2-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="749f2-120">Pour **octroi implicite**, assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="749f2-120">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="749f2-121">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="749f2-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="749f2-122">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="749f2-122">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="749f2-123">Fournissez un **nom** pour l’application (par exemple, **Blazor comptes Microsoft AAD autonomes**).</span><span class="sxs-lookup"><span data-stu-id="749f2-123">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="749f2-124">Dans **types de comptes pris en charge**, sélectionnez **comptes dans n’importe quel annuaire d’organisation**.</span><span class="sxs-lookup"><span data-stu-id="749f2-124">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="749f2-125">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="749f2-125">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="749f2-126">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="749f2-126">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="749f2-127">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="749f2-127">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="749f2-128">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="749f2-128">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="749f2-129">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="749f2-129">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="749f2-130">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="749f2-130">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="749f2-131">Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="749f2-131">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="749f2-132">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="749f2-132">Select **Register**.</span></span>

<span data-ttu-id="749f2-133">Enregistrez l’ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).</span><span class="sxs-lookup"><span data-stu-id="749f2-133">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="749f2-134">Dans  le >  > **site Web** configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="749f2-134">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="749f2-135">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="749f2-135">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="749f2-136">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="749f2-136">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="749f2-137">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="749f2-137">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="749f2-138">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="749f2-138">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="749f2-139">Créez l'application.</span><span class="sxs-lookup"><span data-stu-id="749f2-139">Create the app.</span></span> <span data-ttu-id="749f2-140">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande suivante dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="749f2-140">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="749f2-141">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="749f2-141">Placeholder</span></span>   | <span data-ttu-id="749f2-142">Nom du portail Azure</span><span class="sxs-lookup"><span data-stu-id="749f2-142">Azure portal name</span></span>       | <span data-ttu-id="749f2-143">Exemple</span><span class="sxs-lookup"><span data-stu-id="749f2-143">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="749f2-144">ID d’application (client)</span><span class="sxs-lookup"><span data-stu-id="749f2-144">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="749f2-145">L’emplacement de sortie spécifié avec l’option `-o|--output` crée un dossier de projet s’il n’existe pas et devient partie intégrante du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="749f2-145">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="749f2-146">Dans le Portail Azure, l' **URI de redirection** de la configuration de plateforme de l’application est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="749f2-146">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="749f2-147">Si l’application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="749f2-147">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="749f2-148">Si le port n’a pas été configuré précédemment avec le port connu de l’application, revenez à l’inscription de l’application dans la Portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="749f2-148">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="749f2-149">Après avoir créé l’application, vous devez être en mesure d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="749f2-149">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="749f2-150">Connectez-vous à l’application à l’aide d’un compte Microsoft.</span><span class="sxs-lookup"><span data-stu-id="749f2-150">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="749f2-151">Demander des jetons d’accès pour les API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="749f2-151">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="749f2-152">Pour plus d’informations, consultez :</span><span class="sxs-lookup"><span data-stu-id="749f2-152">For more information, see:</span></span>
  * [<span data-ttu-id="749f2-153">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="749f2-153">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="749f2-154">[Démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="749f2-154">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="749f2-155">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="749f2-155">Authentication package</span></span>

<span data-ttu-id="749f2-156">Quand une application est créée pour utiliser des comptes professionnels ou scolaires ( `SingleOrg` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="749f2-156">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="749f2-157">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="749f2-157">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="749f2-158">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="749f2-158">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="749f2-159">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="749f2-159">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="749f2-160">Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.</span><span class="sxs-lookup"><span data-stu-id="749f2-160">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="749f2-161">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="749f2-161">Authentication service support</span></span>

<span data-ttu-id="749f2-162">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package.</span><span class="sxs-lookup"><span data-stu-id="749f2-162">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="749f2-163">Cette méthode configure tous les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="749f2-163">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="749f2-164">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="749f2-164">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="749f2-165">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="749f2-165">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="749f2-166">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="749f2-166">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="749f2-167">La configuration est fournie par le `wwwroot/appsettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="749f2-167">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="749f2-168">Exemple :</span><span class="sxs-lookup"><span data-stu-id="749f2-168">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="749f2-169">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="749f2-169">Access token scopes</span></span>

<span data-ttu-id="749f2-170">Le Blazor WebAssembly modèle ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="749f2-170">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="749f2-171">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par défaut du <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="749f2-171">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="749f2-172">Spécifier des étendues supplémentaires avec `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="749f2-172">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="749f2-173">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="749f2-173">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="749f2-174">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="749f2-174">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="749f2-175">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="749f2-175">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="749f2-176">Mode de connexion</span><span class="sxs-lookup"><span data-stu-id="749f2-176">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="749f2-177">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="749f2-177">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="749f2-178">Page d'index</span><span class="sxs-lookup"><span data-stu-id="749f2-178">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="749f2-179">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="749f2-179">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="749f2-180">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="749f2-180">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="749f2-181">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="749f2-181">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="749f2-182">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="749f2-182">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="749f2-183">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="749f2-183">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="749f2-184">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="749f2-184">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="749f2-185">Démarrage rapide : Inscrire une application à l’aide de la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="749f2-185">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="749f2-186">Démarrage rapide : Configurer une application pour exposer des API web</span><span class="sxs-lookup"><span data-stu-id="749f2-186">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
