---
title: 'Sécuriser une :::no-loc(Blazor WebAssembly)::: application ASP.net Core autonome avec la bibliothèque d’authentification'
author: guardrex
description: 'Découvrez comment sécuriser une :::no-loc(Blazor WebAssembly)::: application ASP.net Core autonome avec la bibliothèque d’authentification.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 332bf73d21dfe36d2f79e4c016f7f8391c67a1f7
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690364"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="09495-103">Sécuriser une :::no-loc(Blazor WebAssembly)::: application ASP.net Core autonome avec la bibliothèque d’authentification</span><span class="sxs-lookup"><span data-stu-id="09495-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: standalone app with the Authentication library</span></span>

<span data-ttu-id="09495-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="09495-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="09495-105">*Pour Azure Active Directory (AAD) et Azure Active Directory B2C (AAD B2C), ne suivez pas les instructions de cette rubrique. Consultez les rubriques AAD et AAD B2C dans ce nœud de table des matières.*</span><span class="sxs-lookup"><span data-stu-id="09495-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="09495-106">Pour créer une [ :::no-loc(Blazor WebAssembly)::: application autonome](xref:blazor/hosting-models#blazor-webassembly) qui utilise [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) la bibliothèque, suivez les instructions de votre choix d’outils.</span><span class="sxs-lookup"><span data-stu-id="09495-106">To create a [standalone :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="09495-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="09495-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="09495-108">Pour créer un nouveau :::no-loc(Blazor WebAssembly)::: projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="09495-108">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="09495-109">Après avoir choisi le modèle d' **:::no-loc(Blazor WebAssembly)::: application** dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification** .</span><span class="sxs-lookup"><span data-stu-id="09495-109">After choosing the **:::no-loc(Blazor WebAssembly)::: App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication** .</span></span>

1. <span data-ttu-id="09495-110">Sélectionnez **des comptes d’utilisateur individuels** avec l’option **stocker les comptes d’utilisateur dans l’application** pour stocker les utilisateurs au sein de l’application à l’aide du système de ASP.net Core [:::no-loc(Identity):::](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="09495-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="09495-111">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="09495-111">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="09495-112">Créez un :::no-loc(Blazor WebAssembly)::: projet avec un mécanisme d’authentification dans un dossier vide.</span><span class="sxs-lookup"><span data-stu-id="09495-112">Create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="09495-113">Spécifiez le `Individual` mécanisme d’authentification avec l' `-au|--auth` option permettant de stocker les utilisateurs au sein de l’application à l’aide du système de ASP.net Core [:::no-loc(Identity):::](xref:security/authentication/identity) :</span><span class="sxs-lookup"><span data-stu-id="09495-113">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [:::no-loc(Identity):::](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="09495-114">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="09495-114">Placeholder</span></span>  | <span data-ttu-id="09495-115">Exemple</span><span class="sxs-lookup"><span data-stu-id="09495-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `:::no-loc(Blazor):::Sample` |

<span data-ttu-id="09495-116">L’emplacement de sortie spécifié avec l’option `-o|--output` crée un dossier de projet s’il n’existe pas et devient partie intégrante du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="09495-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="09495-117">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="09495-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="09495-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="09495-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="09495-119">Pour créer un nouveau :::no-loc(Blazor WebAssembly)::: projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="09495-119">To create a new :::no-loc(Blazor WebAssembly)::: project with an authentication mechanism:</span></span>

1. <span data-ttu-id="09495-120">Dans l’étape **configurer votre nouvelle :::no-loc(Blazor WebAssembly)::: application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="09495-120">On the **Configure your new :::no-loc(Blazor WebAssembly)::: App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="09495-121">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="09495-121">The app is created for individual users stored in the app with ASP.NET Core [:::no-loc(Identity):::](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="09495-122">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="09495-122">Authentication package</span></span>

<span data-ttu-id="09495-123">Quand une application est créée pour utiliser des comptes d’utilisateur individuels, l’application reçoit automatiquement une référence de package pour le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="09495-123">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="09495-124">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="09495-124">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="09495-125">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="09495-125">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="09495-126">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="09495-126">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="09495-127">Prise en charge du service d’authentification</span><span class="sxs-lookup"><span data-stu-id="09495-127">Authentication service support</span></span>

<span data-ttu-id="09495-128">La prise en charge de l’authentification des utilisateurs est inscrite dans le conteneur de service avec la <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> méthode d’extension fournie par le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) Package.</span><span class="sxs-lookup"><span data-stu-id="09495-128">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="09495-129">Cette méthode permet de configurer les services requis pour que l’application interagisse avec le :::no-loc(Identity)::: fournisseur (IP).</span><span class="sxs-lookup"><span data-stu-id="09495-129">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="09495-130">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="09495-130">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="09495-131">La configuration est fournie par le `wwwroot/appsettings.json` fichier :</span><span class="sxs-lookup"><span data-stu-id="09495-131">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="09495-132">La prise en charge de l’authentification pour les applications autonomes est offerte à l’aide de OpenID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="09495-132">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="09495-133">La <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> méthode accepte un rappel pour configurer les paramètres requis pour authentifier une application à l’aide de OIDC.</span><span class="sxs-lookup"><span data-stu-id="09495-133">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="09495-134">Les valeurs requises pour la configuration de l’application peuvent être obtenues à partir de l’adresse IP conforme à OIDC.</span><span class="sxs-lookup"><span data-stu-id="09495-134">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="09495-135">Obtenez les valeurs lors de l’inscription de l’application, qui se produit généralement dans son portail en ligne.</span><span class="sxs-lookup"><span data-stu-id="09495-135">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="09495-136">Étendues de jeton d’accès</span><span class="sxs-lookup"><span data-stu-id="09495-136">Access token scopes</span></span>

<span data-ttu-id="09495-137">Le :::no-loc(Blazor WebAssembly)::: modèle configure automatiquement les étendues par défaut pour `openid` et `profile` .</span><span class="sxs-lookup"><span data-stu-id="09495-137">The :::no-loc(Blazor WebAssembly)::: template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="09495-138">Le :::no-loc(Blazor WebAssembly)::: modèle ne configure pas automatiquement l’application pour demander un jeton d’accès pour une API sécurisée.</span><span class="sxs-lookup"><span data-stu-id="09495-138">The :::no-loc(Blazor WebAssembly)::: template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="09495-139">Pour approvisionner un jeton d’accès dans le cadre du processus de connexion, ajoutez l’étendue aux étendues de jetons par défaut du <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="09495-139">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="09495-140">Pour plus d’informations, consultez les sections suivantes de l’article relatif aux *scénarios supplémentaires* :</span><span class="sxs-lookup"><span data-stu-id="09495-140">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="09495-141">Demander des jetons d’accès supplémentaires</span><span class="sxs-lookup"><span data-stu-id="09495-141">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="09495-142">Attacher des jetons aux demandes sortantes</span><span class="sxs-lookup"><span data-stu-id="09495-142">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="09495-143">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="09495-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="09495-144">Page d'index</span><span class="sxs-lookup"><span data-stu-id="09495-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="09495-145">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="09495-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="09495-146">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="09495-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="09495-147">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="09495-147">LoginDisplay component</span></span>

<span data-ttu-id="09495-148">Le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ) est rendu dans le `MainLayout` composant ( `Shared/MainLayout.razor` ) et gère les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="09495-148">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="09495-149">Pour les utilisateurs authentifiés :</span><span class="sxs-lookup"><span data-stu-id="09495-149">For authenticated users:</span></span>
  * <span data-ttu-id="09495-150">Affiche le nom d’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="09495-150">Displays the current username.</span></span>
  * <span data-ttu-id="09495-151">Offre un bouton permettant de se déconnecter de l’application.</span><span class="sxs-lookup"><span data-stu-id="09495-151">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="09495-152">Pour les utilisateurs anonymes, offre la possibilité de se connecter.</span><span class="sxs-lookup"><span data-stu-id="09495-152">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.:::no-loc(Identity):::.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="09495-153">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="09495-153">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="09495-154">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="09495-154">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="09495-155">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="09495-155">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
