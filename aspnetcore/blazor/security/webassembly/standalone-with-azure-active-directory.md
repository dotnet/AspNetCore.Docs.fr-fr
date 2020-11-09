---
title: 'Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory'
author: guardrex
description: 'Découvrez comment sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 4e8c22c56b7023301499fd273a9194b8c7b58f3d
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343706"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="7bf31-103">Sécuriser une Blazor WebAssembly application ASP.net Core autonome avec Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="7bf31-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="7bf31-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7bf31-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7bf31-105">Cet article explique comment créer une [ Blazor WebAssembly application autonome](xref:blazor/hosting-models#blazor-webassembly) qui utilise [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) pour l’authentification.</span><span class="sxs-lookup"><span data-stu-id="7bf31-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="7bf31-106">Pour les Blazor WebAssembly applications créées dans Visual Studio qui sont configurées pour prendre en charge les comptes dans un annuaire d’organisation AAD, Visual Studio ne configure pas correctement l’application lors de la génération du projet.</span><span class="sxs-lookup"><span data-stu-id="7bf31-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="7bf31-107">Cela sera résolu dans une prochaine version de Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7bf31-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="7bf31-108">Cet article explique comment créer l’application avec la commande de l’CLI .NET Core `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="7bf31-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="7bf31-109">Si vous préférez créer l’application avec Visual Studio avant la mise à jour de l’IDE pour les modèles les plus récents Blazor dans ASP.NET Core 5,0, reportez-vous à chaque section de cet article et confirmez ou mettez à jour la configuration de l’application une fois que Visual Studio a créé l’application.</span><span class="sxs-lookup"><span data-stu-id="7bf31-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest Blazor templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

<span data-ttu-id="7bf31-110">Inscrire une application AAD dans la **Azure Active Directory** > zone de **inscriptions d’applications** Azure Active Directory de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="7bf31-110">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="7bf31-111">Fournissez un **nom** pour l’application (par exemple, **Blazor AAD autonome** ).</span><span class="sxs-lookup"><span data-stu-id="7bf31-111">Provide a **Name** for the app (for example, **Blazor Standalone AAD** ).</span></span>
1. <span data-ttu-id="7bf31-112">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="7bf31-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="7bf31-113">Vous ne pouvez sélectionner des **comptes dans cet annuaire d’organisation que** pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="7bf31-113">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="7bf31-114">Définissez la liste déroulante **URI de redirection** sur **une application à page unique (Spa)** et fournissez l’URI de redirection suivante : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="7bf31-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="7bf31-115">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="7bf31-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="7bf31-116">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="7bf31-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="7bf31-117">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="7bf31-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="7bf31-118">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="7bf31-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="7bf31-119">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="7bf31-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="7bf31-120">Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="7bf31-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="7bf31-121">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="7bf31-121">Select **Register**.</span></span>

<span data-ttu-id="7bf31-122">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7bf31-122">Record the following information:</span></span>

* <span data-ttu-id="7bf31-123">ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="7bf31-123">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="7bf31-124">ID de répertoire (locataire) (par exemple, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="7bf31-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="7bf31-125">Dans configurations de plateforme **d’authentification** , > **Platform configurations** > **application à page unique (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="7bf31-125">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="7bf31-126">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="7bf31-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="7bf31-127">Pour **octroi implicite** , assurez-vous que les cases à cocher pour les **jetons d’accès** et les **jetons d’ID** ne sont **pas** sélectionnées.</span><span class="sxs-lookup"><span data-stu-id="7bf31-127">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="7bf31-128">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="7bf31-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="7bf31-129">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="7bf31-129">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="7bf31-130">Inscrire une application AAD dans la **Azure Active Directory** > zone de **inscriptions d’applications** Azure Active Directory de l’portail Azure :</span><span class="sxs-lookup"><span data-stu-id="7bf31-130">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="7bf31-131">Fournissez un **nom** pour l’application (par exemple, **Blazor AAD autonome** ).</span><span class="sxs-lookup"><span data-stu-id="7bf31-131">Provide a **Name** for the app (for example, **Blazor Standalone AAD** ).</span></span>
1. <span data-ttu-id="7bf31-132">Choisissez un **type de compte pris en charge**.</span><span class="sxs-lookup"><span data-stu-id="7bf31-132">Choose a **Supported account types**.</span></span> <span data-ttu-id="7bf31-133">Vous ne pouvez sélectionner des **comptes dans cet annuaire d’organisation que** pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="7bf31-133">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="7bf31-134">Laissez la liste déroulante **URI de redirection** définie sur **Web** et indiquez l’URI de redirection suivant : `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="7bf31-134">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="7bf31-135">Le port par défaut pour une application s’exécutant sur Kestrel est 5001.</span><span class="sxs-lookup"><span data-stu-id="7bf31-135">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="7bf31-136">Si l’application est exécutée sur un autre port Kestrel, utilisez le port de l’application.</span><span class="sxs-lookup"><span data-stu-id="7bf31-136">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="7bf31-137">Par IIS Express, le port généré de manière aléatoire pour l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="7bf31-137">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="7bf31-138">Étant donné que l’application n’existe pas à ce stade et que le port IIS Express n’est pas connu, revenez à cette étape après la création de l’application et mettez à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="7bf31-138">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="7bf31-139">Une remarque s’affiche plus loin dans cette rubrique pour rappeler IIS Express utilisateurs de mettre à jour l’URI de redirection.</span><span class="sxs-lookup"><span data-stu-id="7bf31-139">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="7bf31-140">Désactivez **la case** > à cocher **accorder le consentement de l’administrateur aux autorisations OpenID et offline_access** .</span><span class="sxs-lookup"><span data-stu-id="7bf31-140">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="7bf31-141">Sélectionnez **Inscription**.</span><span class="sxs-lookup"><span data-stu-id="7bf31-141">Select **Register**.</span></span>

<span data-ttu-id="7bf31-142">Notez les informations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7bf31-142">Record the following information:</span></span>

* <span data-ttu-id="7bf31-143">ID de l’application (client) (par exemple, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="7bf31-143">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="7bf31-144">ID de répertoire (locataire) (par exemple, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="7bf31-144">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="7bf31-145">Dans **Authentication** le > **Platform configurations** > **site Web** configurations de la plateforme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="7bf31-145">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="7bf31-146">Confirmez que l' **URI de redirection** de `https://localhost:{PORT}/authentication/login-callback` est présent.</span><span class="sxs-lookup"><span data-stu-id="7bf31-146">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="7bf31-147">Pour **octroi implicite** , activez les cases à cocher pour les **jetons d’accès** et les **jetons d’ID**.</span><span class="sxs-lookup"><span data-stu-id="7bf31-147">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="7bf31-148">Les valeurs par défaut restantes pour l’application sont acceptables pour cette expérience.</span><span class="sxs-lookup"><span data-stu-id="7bf31-148">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="7bf31-149">Sélectionnez le bouton **Enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="7bf31-149">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="7bf31-150">Créez l’application dans un dossier vide.</span><span class="sxs-lookup"><span data-stu-id="7bf31-150">Create the app in an empty folder.</span></span> <span data-ttu-id="7bf31-151">Remplacez les espaces réservés dans la commande suivante par les informations enregistrées précédemment et exécutez la commande dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="7bf31-151">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="7bf31-152">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="7bf31-152">Placeholder</span></span>   | <span data-ttu-id="7bf31-153">Nom du portail Azure</span><span class="sxs-lookup"><span data-stu-id="7bf31-153">Azure portal name</span></span>       | <span data-ttu-id="7bf31-154">Exemple</span><span class="sxs-lookup"><span data-stu-id="7bf31-154">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="7bf31-155">ID d’application (client)</span><span class="sxs-lookup"><span data-stu-id="7bf31-155">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="7bf31-156">ID de l’annuaire (locataire)</span><span class="sxs-lookup"><span data-stu-id="7bf31-156">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="7bf31-157">L’emplacement de sortie spécifié avec l’option `-o|--output` crée un dossier de projet s’il n’existe pas et devient partie intégrante du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="7bf31-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="7bf31-158">Dans le Portail Azure, l' **URI de redirection** de la configuration de plateforme de l’application est configuré pour le port 5001 pour les applications qui s’exécutent sur le serveur Kestrel avec les paramètres par défaut.</span><span class="sxs-lookup"><span data-stu-id="7bf31-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="7bf31-159">Si l’application est exécutée sur un port IIS Express aléatoire, le port de l’application se trouve dans les propriétés de l’application dans le panneau **débogage** .</span><span class="sxs-lookup"><span data-stu-id="7bf31-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="7bf31-160">Si le port n’a pas été configuré précédemment avec le port connu de l’application, revenez à l’inscription de l’application dans la Portail Azure et mettez à jour l’URI de redirection avec le port approprié.</span><span class="sxs-lookup"><span data-stu-id="7bf31-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="7bf31-161">Après avoir créé l’application, vous devez être en mesure d’effectuer les opérations suivantes :</span><span class="sxs-lookup"><span data-stu-id="7bf31-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="7bf31-162">Connectez-vous à l’application à l’aide d’un compte d’utilisateur AAD.</span><span class="sxs-lookup"><span data-stu-id="7bf31-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="7bf31-163">Demander des jetons d’accès pour les API Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7bf31-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="7bf31-164">Pour plus d'informations, consultez les pages suivantes :</span><span class="sxs-lookup"><span data-stu-id="7bf31-164">For more information, see:</span></span>
  * [<span data-ttu-id="7bf31-165">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="7bf31-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="7bf31-166">[Démarrage rapide : configurer une application pour exposer des API Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="7bf31-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="7bf31-167">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="7bf31-167">Authentication package</span></span>

<span data-ttu-id="7bf31-168">Quand une application est créée pour utiliser des comptes professionnels ou scolaires ( `SingleOrg` ), l’application reçoit automatiquement une référence de package pour la [bibliothèque d’authentification Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ).</span><span class="sxs-lookup"><span data-stu-id="7bf31-168">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="7bf31-169">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="7bf31-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7bf31-170">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="7bf31-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="7bf31-171">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span><span class="sxs-lookup"><span data-stu-id="7bf31-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="7bf31-172">Le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package ajoute transitivement le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package à l’application.</span><span class="sxs-lookup"><span data-stu-id="7bf31-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="7bf31-173">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="7bf31-173">Authentication service support</span></span>

<span data-ttu-id="7bf31-174">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode d’extension fournie par le [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) Package.</span><span class="sxs-lookup"><span data-stu-id="7bf31-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="7bf31-175">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le Identity fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="7bf31-175">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7bf31-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="7bf31-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="7bf31-177">La <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application.</span><span class="sxs-lookup"><span data-stu-id="7bf31-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="7bf31-178">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de la configuration AAD lorsque vous inscrivez l’application.</span><span class="sxs-lookup"><span data-stu-id="7bf31-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="7bf31-179">La configuration est fournie par le `wwwroot/appsettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="7bf31-179">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="7bf31-180">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7bf31-180">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="7bf31-181">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="7bf31-181">Access token scopes</span></span>

<span data-ttu-id="7bf31-182">Le Blazor WebAssembly modèle ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="7bf31-182">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="7bf31-183">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jeton d’accès par défaut du <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="7bf31-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="7bf31-184">Spécifier des étendues supplémentaires avec `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="7bf31-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="7bf31-185">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="7bf31-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="7bf31-186">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7bf31-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="7bf31-187">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="7bf31-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="7bf31-188">Mode de connexion</span><span class="sxs-lookup"><span data-stu-id="7bf31-188">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="7bf31-189">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="7bf31-189">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="7bf31-190">Page d'index</span><span class="sxs-lookup"><span data-stu-id="7bf31-190">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="7bf31-191">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="7bf31-191">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="7bf31-192">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="7bf31-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="7bf31-193">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="7bf31-193">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="7bf31-194">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="7bf31-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7bf31-195">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7bf31-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="7bf31-196">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="7bf31-196">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="7bf31-197">Documentation sur la plateforme d’identités Microsoft</span><span class="sxs-lookup"><span data-stu-id="7bf31-197">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
