---
title: Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory
author: guardrex
description: Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory.
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: b44c5372d694dcc16ff66e24233171e3320d7294
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900898"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="50920-103">Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="50920-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="50920-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50920-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="50920-105">Pour créer une [ Blazor WebAssembly application autonome](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification :</span><span class="sxs-lookup"><span data-stu-id="50920-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="50920-106">[Créez un client et une application Web AAD](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="50920-106">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="50920-107">Inscrire une application AAD dans la **Azure Active Directory**  >  zone de**inscriptions d’applications** Azure Active Directory de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="50920-107">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="50920-108">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor AAD autonome\*\*).</span><span class="sxs-lookup"><span data-stu-id="50920-108">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="50920-109">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="50920-109">Choose a **Supported account types**.</span></span> <span data-ttu-id="50920-110">Vous ne pouvez sélectionner des **comptes dans cet annuaire d’organisation que** pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="50920-110">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="50920-111">Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="50920-111">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="50920-112">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="50920-112">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="50920-113">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="50920-113">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="50920-114">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="50920-114">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="50920-115">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="50920-115">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="50920-116">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="50920-116">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="50920-117">Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="50920-117">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="50920-118">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="50920-118">Select **Register**.</span></span>

<span data-ttu-id="50920-119">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="50920-119">Record the following information:</span></span>

* <span data-ttu-id="50920-120">ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="50920-120">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="50920-121">ID de répertoire (locataire) (par exemple, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="50920-121">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="50920-122">Dans configurations de plateforme **d’authentification** , > **Platform configurations** > **application à page unique (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="50920-122">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="50920-123">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="50920-123">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="50920-124">Pour **octroi implicite**, assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="50920-124">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="50920-125">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="50920-125">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="50920-126">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="50920-126">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="50920-127">Fournissez un **nom** pour l’application (par exemple, \*\* Blazor AAD autonome\*\*).</span><span class="sxs-lookup"><span data-stu-id="50920-127">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="50920-128">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="50920-128">Choose a **Supported account types**.</span></span> <span data-ttu-id="50920-129">Vous ne pouvez sélectionner des **comptes dans cet annuaire d’organisation que** pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="50920-129">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="50920-130">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="50920-130">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="50920-131">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="50920-131">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="50920-132">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="50920-132">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="50920-133">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="50920-133">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="50920-134">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="50920-134">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="50920-135">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="50920-135">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="50920-136">Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="50920-136">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="50920-137">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="50920-137">Select **Register**.</span></span>

<span data-ttu-id="50920-138">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="50920-138">Record the following information:</span></span>

* <span data-ttu-id="50920-139">ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="50920-139">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="50920-140">ID de répertoire (locataire) (par exemple, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="50920-140">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="50920-141">Dans **Authentication** le > **Platform configurations** > **site Web**configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="50920-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="50920-142">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="50920-142">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="50920-143">Pour **octroi implicite**, activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="50920-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="50920-144">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="50920-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="50920-145">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="50920-145">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="50920-146">Créez l’application dans un dossier vide.</span><span class="sxs-lookup"><span data-stu-id="50920-146">Create the app in an empty folder.</span></span> <span data-ttu-id="50920-147">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="50920-147">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="50920-148">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="50920-148">Placeholder</span></span>   | <span data-ttu-id="50920-149">Nom du portail Azure</span><span class="sxs-lookup"><span data-stu-id="50920-149">Azure portal name</span></span>       | <span data-ttu-id="50920-150">Exemple</span><span class="sxs-lookup"><span data-stu-id="50920-150">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="50920-151">ID d’application (client)</span><span class="sxs-lookup"><span data-stu-id="50920-151">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="50920-152">ID de l’annuaire (locataire)</span><span class="sxs-lookup"><span data-stu-id="50920-152">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="50920-153">L’emplacement de sortie spécifié avec l' `-o|--output` option crée un dossier de projet s’il n’existe pas et fait partie du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="50920-153">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="50920-154">Dans le Portail Azure, l' **URI de redirection** de la configuration de plateforme de l’application est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="50920-154">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="50920-155">Si l’application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="50920-155">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="50920-156">Si le port n’a pas été configuré précédemment avec le port connu de l’application, revenez à l’inscription de l’application dans la Portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="50920-156">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="50920-157">Après avoir créé l’application, vous devez être en mesure d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="50920-157">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="50920-158">Connectez-vous à l’application à l’aide d’un compte d’utilisateur AAD.</span><span class="sxs-lookup"><span data-stu-id="50920-158">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="50920-159">Demander des jetons d’accès pour les API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="50920-159">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="50920-160">Pour plus d'informations, voir :</span><span class="sxs-lookup"><span data-stu-id="50920-160">For more information, see:</span></span>
  * [<span data-ttu-id="50920-161">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="50920-161">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="50920-162">[Démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="50920-162">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="50920-163">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="50920-163">Authentication package</span></span>

<span data-ttu-id="50920-164">Quand une application est créée pour utiliser des comptes professionnels ou scolaires ( `SingleOrg` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="50920-164">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="50920-165">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="50920-165">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="50920-166">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="50920-166">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="50920-167">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="50920-167">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="50920-168">Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.</span><span class="sxs-lookup"><span data-stu-id="50920-168">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="50920-169">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="50920-169">Authentication service support</span></span>

<span data-ttu-id="50920-170">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package.</span><span class="sxs-lookup"><span data-stu-id="50920-170">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="50920-171">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="50920-171">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="50920-172">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="50920-172">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="50920-173">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="50920-173">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="50920-174">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="50920-174">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="50920-175">La configuration est fournie par le `wwwroot/appsettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="50920-175">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="50920-176">Exemple :</span><span class="sxs-lookup"><span data-stu-id="50920-176">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="50920-177">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="50920-177">Access token scopes</span></span>

<span data-ttu-id="50920-178">Le Blazor WebAssembly modèle ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="50920-178">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="50920-179">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par défaut du <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="50920-179">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="50920-180">Spécifier des étendues supplémentaires avec `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="50920-180">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="50920-181">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="50920-181">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="50920-182">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="50920-182">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="50920-183">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="50920-183">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="50920-184">Mode de connexion</span><span class="sxs-lookup"><span data-stu-id="50920-184">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="50920-185">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="50920-185">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="50920-186">Page d'index</span><span class="sxs-lookup"><span data-stu-id="50920-186">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="50920-187">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="50920-187">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="50920-188">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="50920-188">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="50920-189">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="50920-189">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="50920-190">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="50920-190">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="50920-191">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="50920-191">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="50920-192">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="50920-192">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="50920-193">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="50920-193">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
