---
title: Sécuriser une application ASP.NET Core hébergée Blazor WebAssembly avec le Identity serveur
author: guardrex
description: Découvrez comment sécuriser une application ASP.NET Core hébergée Blazor WebAssembly avec le Identity serveur.
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: d35dd0acf626a6305f00e295e7918c82c7d6a912
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658701"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="be935-103">Sécuriser une Blazor WebAssembly application hébergée ASP.net core avec le Identity serveur</span><span class="sxs-lookup"><span data-stu-id="be935-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="be935-104">Par [Javier Calvarro Nelson](https://github.com/javiercn) et [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="be935-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="be935-105">Cet article explique comment créer une [ Blazor WebAssembly application hébergée](xref:blazor/hosting-models#blazor-webassembly) qui utilise le [ Identity serveur](https://identityserver.io/) pour authentifier les utilisateurs et les appels d’API.</span><span class="sxs-lookup"><span data-stu-id="be935-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="be935-106">Pour configurer une application autonome ou hébergée Blazor WebAssembly afin d’utiliser une instance de serveur externe existante Identity , suivez les instructions dans <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="be935-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="be935-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be935-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="be935-108">Pour créer un nouveau Blazor WebAssembly projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="be935-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="be935-109">Après avoir choisi le modèle d' **Blazor WebAssembly application** dans la boîte de dialogue **créer une application Web ASP.net Core** , sélectionnez **modifier** sous **authentification**.</span><span class="sxs-lookup"><span data-stu-id="be935-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="be935-110">Sélectionnez **des comptes d’utilisateur individuels** avec l’option **stocker les comptes d’utilisateur dans l’application** pour stocker les utilisateurs au sein de l’application à l’aide du système de ASP.net Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="be935-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="be935-111">Activez la case à cocher **ASP.net Core hébergée** dans la section **avancé** .</span><span class="sxs-lookup"><span data-stu-id="be935-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="be935-112">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="be935-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="be935-113">Pour créer un Blazor WebAssembly projet avec un mécanisme d’authentification dans un dossier vide, spécifiez le `Individual` mécanisme d’authentification avec l' `-au|--auth` option permettant de stocker les utilisateurs au sein de l’application à l’aide du système de ASP.net Core [Identity](xref:security/authentication/identity) :</span><span class="sxs-lookup"><span data-stu-id="be935-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="be935-114">Espace réservé</span><span class="sxs-lookup"><span data-stu-id="be935-114">Placeholder</span></span>  | <span data-ttu-id="be935-115">Exemple</span><span class="sxs-lookup"><span data-stu-id="be935-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="be935-116">L’emplacement de sortie spécifié avec l’option `-o|--output` crée un dossier de projet s’il n’existe pas et devient partie intégrante du nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="be935-117">Pour plus d’informations, consultez la [`dotnet new`](/dotnet/core/tools/dotnet-new) commande dans le guide .net core.</span><span class="sxs-lookup"><span data-stu-id="be935-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="be935-118">Visual Studio pour Mac</span><span class="sxs-lookup"><span data-stu-id="be935-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="be935-119">Pour créer un nouveau Blazor WebAssembly projet avec un mécanisme d’authentification :</span><span class="sxs-lookup"><span data-stu-id="be935-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="be935-120">Dans l’étape **configurer votre nouvelle Blazor WebAssembly application** , sélectionnez **authentification individuelle (dans l’application)** dans la liste déroulante **authentification** .</span><span class="sxs-lookup"><span data-stu-id="be935-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="be935-121">L’application est créée pour les utilisateurs individuels stockés dans l’application avec ASP.NET Core [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="be935-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="be935-122">Activez la case à cocher **ASP.net Core hébergé** .</span><span class="sxs-lookup"><span data-stu-id="be935-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="be935-123">*`Server`* configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="be935-123">*`Server`* app configuration</span></span>

<span data-ttu-id="be935-124">Les sections suivantes décrivent les ajouts au projet lorsque la prise en charge de l’authentification est incluse.</span><span class="sxs-lookup"><span data-stu-id="be935-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="be935-125">Classe de démarrage</span><span class="sxs-lookup"><span data-stu-id="be935-125">Startup class</span></span>

<span data-ttu-id="be935-126">La `Startup` classe a les ajouts suivants.</span><span class="sxs-lookup"><span data-stu-id="be935-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="be935-127">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="be935-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="be935-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="be935-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="be935-129">IdentityServeur avec une <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’assistance supplémentaire qui définit les conventions de ASP.net Core par défaut sur le Identity serveur :</span><span class="sxs-lookup"><span data-stu-id="be935-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="be935-130">Authentification avec une <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’assistance supplémentaire qui configure l’application pour valider les jetons JWT générés par le Identity serveur :</span><span class="sxs-lookup"><span data-stu-id="be935-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="be935-131">Dans `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="be935-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="be935-132">L' Identity intergiciel (middleware) de serveur expose les points de terminaison OpenID Connect (OIDC) :</span><span class="sxs-lookup"><span data-stu-id="be935-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="be935-133">L’intergiciel d’authentification est responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur dans le contexte de la requête :</span><span class="sxs-lookup"><span data-stu-id="be935-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="be935-134">L’intergiciel d’autorisation active les fonctionnalités d’autorisation :</span><span class="sxs-lookup"><span data-stu-id="be935-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="be935-135">Azure App Service sur Linux</span><span class="sxs-lookup"><span data-stu-id="be935-135">Azure App Service on Linux</span></span>

<span data-ttu-id="be935-136">Spécifiez l’émetteur de manière explicite lors du déploiement sur Azure App Service sur Linux.</span><span class="sxs-lookup"><span data-stu-id="be935-136">Specify the issuer explicitly when deploying to Azure App Service on Linux.</span></span> <span data-ttu-id="be935-137">Pour plus d'informations, consultez <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="be935-137">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

### <a name="addapiauthorization"></a><span data-ttu-id="be935-138">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="be935-138">AddApiAuthorization</span></span>

<span data-ttu-id="be935-139">La <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> méthode d’assistance configure [ Identity Server](https://identityserver.io/) pour les scénarios de ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="be935-139">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="be935-140">IdentityLe serveur est un Framework puissant et extensible pour gérer les problèmes de sécurité des applications.</span><span class="sxs-lookup"><span data-stu-id="be935-140">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="be935-141">IdentityLe serveur expose une complexité inutile pour les scénarios les plus courants.</span><span class="sxs-lookup"><span data-stu-id="be935-141">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="be935-142">Par conséquent, un ensemble de conventions et d’options de configuration est fourni, que nous considérons comme un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="be935-142">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="be935-143">Une fois vos besoins d’authentification modifiés, toute la puissance du Identity serveur est disponible pour personnaliser l’authentification en fonction des exigences d’une application.</span><span class="sxs-lookup"><span data-stu-id="be935-143">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="be935-144">Ajouter Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="be935-144">AddIdentityServerJwt</span></span>

<span data-ttu-id="be935-145">La <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> méthode d’assistance configure un modèle de stratégie pour l’application en tant que gestionnaire d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="be935-145">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="be935-146">La stratégie est configurée pour autoriser Identity à gérer toutes les demandes routées vers n’importe quel sous-chemin dans l' Identity espace d’URL `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="be935-146">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="be935-147">Le <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> gère toutes les autres requêtes.</span><span class="sxs-lookup"><span data-stu-id="be935-147">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="be935-148">En outre, cette méthode :</span><span class="sxs-lookup"><span data-stu-id="be935-148">Additionally, this method:</span></span>

* <span data-ttu-id="be935-149">Inscrit une `{APPLICATION NAME}API` ressource API avec Identity le serveur avec une étendue par défaut de `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="be935-149">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="be935-150">Configure l’intergiciel de jeton de porteur JWT pour valider les jetons émis par Identity le serveur pour l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-150">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="be935-151">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="be935-151">WeatherForecastController</span></span>

<span data-ttu-id="be935-152">Dans `WeatherForecastController` ( `Controllers/WeatherForecastController.cs` ), l' [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribut est appliqué à la classe.</span><span class="sxs-lookup"><span data-stu-id="be935-152">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="be935-153">L’attribut indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource.</span><span class="sxs-lookup"><span data-stu-id="be935-153">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="be935-154">La stratégie d’autorisation par défaut est configurée pour utiliser le schéma d’authentification par défaut, qui est configuré par <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="be935-154">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="be935-155">La méthode d’assistance configure <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> en tant que gestionnaire par défaut pour les demandes à l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-155">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="be935-156">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="be935-156">ApplicationDbContext</span></span>

<span data-ttu-id="be935-157">Dans le `ApplicationDbContext` ( `Data/ApplicationDbContext.cs` ), <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> s’étend pour inclure le schéma pour le Identity serveur.</span><span class="sxs-lookup"><span data-stu-id="be935-157">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="be935-158"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> est dérivé de <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span><span class="sxs-lookup"><span data-stu-id="be935-158"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="be935-159">Pour obtenir le contrôle total du schéma de base de données, héritez de l’une des Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes disponibles et configurez le contexte pour inclure le Identity schéma en appelant `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` dans la <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> méthode.</span><span class="sxs-lookup"><span data-stu-id="be935-159">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="be935-160">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="be935-160">OidcConfigurationController</span></span>

<span data-ttu-id="be935-161">Dans le `OidcConfigurationController` ( `Controllers/OidcConfigurationController.cs` ), le point de terminaison client est approvisionné pour servir les paramètres OIDC.</span><span class="sxs-lookup"><span data-stu-id="be935-161">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="be935-162">Paramètres de l’application</span><span class="sxs-lookup"><span data-stu-id="be935-162">App settings</span></span>

<span data-ttu-id="be935-163">Dans le fichier de paramètres d’application ( `appsettings.json` ) à la racine du projet, la `IdentityServer` section décrit la liste des clients configurés.</span><span class="sxs-lookup"><span data-stu-id="be935-163">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="be935-164">Dans l’exemple suivant, il existe un seul client.</span><span class="sxs-lookup"><span data-stu-id="be935-164">In the following example, there's a single client.</span></span> <span data-ttu-id="be935-165">Le nom du client correspond au nom de l’application et est mappé par Convention au `ClientId` paramètre OAuth.</span><span class="sxs-lookup"><span data-stu-id="be935-165">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="be935-166">Le profil indique le type d’application en cours de configuration.</span><span class="sxs-lookup"><span data-stu-id="be935-166">The profile indicates the app type being configured.</span></span> <span data-ttu-id="be935-167">Le profil est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur.</span><span class="sxs-lookup"><span data-stu-id="be935-167">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="be935-168">L’espace réservé `{APP ASSEMBLY}` est le nom de l’assembly de l’application (par exemple, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="be935-168">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="be935-169">*`Client`* configuration de l’application</span><span class="sxs-lookup"><span data-stu-id="be935-169">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="be935-170">Package d’authentification</span><span class="sxs-lookup"><span data-stu-id="be935-170">Authentication package</span></span>

<span data-ttu-id="be935-171">Quand une application est créée pour utiliser des comptes d’utilisateur individuels ( `Individual` ), l’application reçoit automatiquement une référence de package pour le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package dans le fichier projet de l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-171">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="be935-172">Le package fournit un ensemble de primitives qui aident l’application à authentifier les utilisateurs et à obtenir des jetons pour appeler des API protégées.</span><span class="sxs-lookup"><span data-stu-id="be935-172">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="be935-173">Si vous ajoutez l’authentification à une application, ajoutez manuellement le package au fichier projet de l’application :</span><span class="sxs-lookup"><span data-stu-id="be935-173">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="be935-174">Pour l’espace réservé `{VERSION}` , la dernière version stable du package qui correspond à la version du Framework partagé de l’application est disponible dans l' **historique des versions** du package sur [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span><span class="sxs-lookup"><span data-stu-id="be935-174">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="be935-175">`HttpClient` configuré</span><span class="sxs-lookup"><span data-stu-id="be935-175">`HttpClient` configuration</span></span>

<span data-ttu-id="be935-176">Dans `Program.Main` ( `Program.cs` ), un nommé <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ) est configuré pour fournir des <xref:System.Net.Http.HttpClient> instances qui incluent des jetons d’accès lors de l’exécution de requêtes à l’API serveur :</span><span class="sxs-lookup"><span data-stu-id="be935-176">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="be935-177">Si vous configurez une Blazor WebAssembly application pour qu’elle utilise une Identity instance de serveur existante qui ne fait pas partie d’une solution hébergée Blazor , modifiez l' <xref:System.Net.Http.HttpClient> inscription de l’adresse de base <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) en spécifiant l’URL du point de terminaison d’autorisation d’API de l’application serveur.</span><span class="sxs-lookup"><span data-stu-id="be935-177">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="be935-178">Prise en charge des autorisations d’API</span><span class="sxs-lookup"><span data-stu-id="be935-178">API authorization support</span></span>

<span data-ttu-id="be935-179">La prise en charge de l’authentification des utilisateurs est insérée dans le conteneur de service par la méthode d’extension fournie dans le [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) Package.</span><span class="sxs-lookup"><span data-stu-id="be935-179">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="be935-180">Cette méthode Configure les services requis par l’application pour interagir avec le système d’autorisation existant.</span><span class="sxs-lookup"><span data-stu-id="be935-180">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="be935-181">Par défaut, la configuration de l’application est chargée par Convention à partir de `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="be935-181">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="be935-182">Par Convention, l’ID client est défini sur le nom de l’assembly de l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-182">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="be935-183">Cette URL peut être modifiée pour pointer vers un point de terminaison distinct en appelant la surcharge avec des options.</span><span class="sxs-lookup"><span data-stu-id="be935-183">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="be935-184">Fichier d’importation</span><span class="sxs-lookup"><span data-stu-id="be935-184">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="be935-185">Page d'index</span><span class="sxs-lookup"><span data-stu-id="be935-185">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="be935-186">Composant d’application</span><span class="sxs-lookup"><span data-stu-id="be935-186">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="be935-187">Composant RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="be935-187">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="be935-188">Composant LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="be935-188">LoginDisplay component</span></span>

<span data-ttu-id="be935-189">Le `LoginDisplay` composant ( `Shared/LoginDisplay.razor` ) est rendu dans le `MainLayout` composant ( `Shared/MainLayout.razor` ) et gère les comportements suivants :</span><span class="sxs-lookup"><span data-stu-id="be935-189">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="be935-190">Pour les utilisateurs authentifiés :</span><span class="sxs-lookup"><span data-stu-id="be935-190">For authenticated users:</span></span>
  * <span data-ttu-id="be935-191">Affiche le nom de l’utilisateur actuel.</span><span class="sxs-lookup"><span data-stu-id="be935-191">Displays the current user name.</span></span>
  * <span data-ttu-id="be935-192">Propose un lien vers la page de profil utilisateur dans ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="be935-192">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="be935-193">Offre un bouton permettant de se déconnecter de l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-193">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="be935-194">Pour les utilisateurs anonymes :</span><span class="sxs-lookup"><span data-stu-id="be935-194">For anonymous users:</span></span>
  * <span data-ttu-id="be935-195">Offre la possibilité de s’inscrire.</span><span class="sxs-lookup"><span data-stu-id="be935-195">Offers the option to register.</span></span>
  * <span data-ttu-id="be935-196">Offre la possibilité de se connecter.</span><span class="sxs-lookup"><span data-stu-id="be935-196">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
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

### <a name="authentication-component"></a><span data-ttu-id="be935-197">Composant d’authentification</span><span class="sxs-lookup"><span data-stu-id="be935-197">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="be935-198">Composant FetchData</span><span class="sxs-lookup"><span data-stu-id="be935-198">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="be935-199">Exécuter l’application</span><span class="sxs-lookup"><span data-stu-id="be935-199">Run the app</span></span>

<span data-ttu-id="be935-200">Exécutez l’application à partir du projet serveur.</span><span class="sxs-lookup"><span data-stu-id="be935-200">Run the app from the Server project.</span></span> <span data-ttu-id="be935-201">Lorsque vous utilisez Visual Studio, vous pouvez :</span><span class="sxs-lookup"><span data-stu-id="be935-201">When using Visual Studio, either:</span></span>

* <span data-ttu-id="be935-202">Définissez la liste déroulante des **projets de démarrage** dans la barre d’outils sur l' *application API serveur* , puis sélectionnez le bouton **exécuter** .</span><span class="sxs-lookup"><span data-stu-id="be935-202">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="be935-203">Sélectionnez le projet serveur dans **Explorateur de solutions** , puis cliquez sur le bouton **exécuter** dans la barre d’outils ou démarrez l’application à partir du menu **Déboguer** .</span><span class="sxs-lookup"><span data-stu-id="be935-203">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="be935-204">Nom et revendication de rôle avec autorisation d’API</span><span class="sxs-lookup"><span data-stu-id="be935-204">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="be935-205">Fabrique d’utilisateur personnalisée</span><span class="sxs-lookup"><span data-stu-id="be935-205">Custom user factory</span></span>

<span data-ttu-id="be935-206">Dans l' *`Client`* application, créez une fabrique d’utilisateurs personnalisée.</span><span class="sxs-lookup"><span data-stu-id="be935-206">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="be935-207">Identity Le serveur envoie plusieurs rôles sous forme de tableau JSON dans une seule `role` revendication.</span><span class="sxs-lookup"><span data-stu-id="be935-207">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="be935-208">Un rôle unique est envoyé sous la forme d’une valeur de chaîne dans la revendication.</span><span class="sxs-lookup"><span data-stu-id="be935-208">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="be935-209">La fabrique crée une `role` revendication individuelle pour chacun des rôles de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="be935-209">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="be935-210">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="be935-210">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="be935-211">Dans l' *`Client`* application, inscrivez la fabrique dans `Program.Main` ( `Program.cs` ) :</span><span class="sxs-lookup"><span data-stu-id="be935-211">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="be935-212">Dans l' *`Server`* application, appelez <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> sur le Identity générateur, qui ajoute des services liés aux rôles :</span><span class="sxs-lookup"><span data-stu-id="be935-212">In the *`Server`* app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="be935-213">Configurer le Identity serveur</span><span class="sxs-lookup"><span data-stu-id="be935-213">Configure Identity Server</span></span>

<span data-ttu-id="be935-214">Utilisez l' **une** des approches suivantes :</span><span class="sxs-lookup"><span data-stu-id="be935-214">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="be935-215">Options d’autorisation d’API</span><span class="sxs-lookup"><span data-stu-id="be935-215">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="be935-216">Service de profil</span><span class="sxs-lookup"><span data-stu-id="be935-216">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="be935-217">Options d’autorisation d’API</span><span class="sxs-lookup"><span data-stu-id="be935-217">API authorization options</span></span>

<span data-ttu-id="be935-218">Dans l' *`Server`* application :</span><span class="sxs-lookup"><span data-stu-id="be935-218">In the *`Server`* app:</span></span>

* <span data-ttu-id="be935-219">Configurez Identity le serveur pour placer les `name` `role` revendications et dans le jeton d’ID et le jeton d’accès.</span><span class="sxs-lookup"><span data-stu-id="be935-219">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="be935-220">Empêche le mappage par défaut des rôles dans le gestionnaire de jetons JWT.</span><span class="sxs-lookup"><span data-stu-id="be935-220">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="be935-221">Service de profil</span><span class="sxs-lookup"><span data-stu-id="be935-221">Profile Service</span></span>

<span data-ttu-id="be935-222">Dans l' *`Server`* application, créez une `ProfileService` implémentation.</span><span class="sxs-lookup"><span data-stu-id="be935-222">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="be935-223">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="be935-223">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="be935-224">Dans l' *`Server`* application, inscrivez le service de profil dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="be935-224">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="be935-225">Utiliser des mécanismes d’autorisation</span><span class="sxs-lookup"><span data-stu-id="be935-225">Use authorization mechanisms</span></span>

<span data-ttu-id="be935-226">Dans l' *`Client`* application, les approches d’autorisation des composants sont fonctionnelles à ce stade.</span><span class="sxs-lookup"><span data-stu-id="be935-226">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="be935-227">L’un des mécanismes d’autorisation des composants peut utiliser un rôle pour autoriser l’utilisateur :</span><span class="sxs-lookup"><span data-stu-id="be935-227">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="be935-228">[ `AuthorizeView` composant](xref:blazor/security/index#authorizeview-component) (exemple : `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="be935-228">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="be935-229">[ `[Authorize]` directive d’attribut](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (exemple : `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="be935-229">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="be935-230">[Logique procédurale](xref:blazor/security/index#procedural-logic) (exemple : `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="be935-230">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="be935-231">Plusieurs tests de rôle sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="be935-231">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="be935-232">`User.Identity.Name` est renseigné dans l' *`Client`* application avec le nom d’utilisateur de l’utilisateur, qui est généralement son adresse e-mail de connexion.</span><span class="sxs-lookup"><span data-stu-id="be935-232">`User.Identity.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain-and-certificate"></a><span data-ttu-id="be935-233">Héberger dans Azure App Service avec un domaine et un certificat personnalisés</span><span class="sxs-lookup"><span data-stu-id="be935-233">Host in Azure App Service with a custom domain and certificate</span></span>

<span data-ttu-id="be935-234">Les instructions suivantes expliquent :</span><span class="sxs-lookup"><span data-stu-id="be935-234">The following guidance explains:</span></span>

* <span data-ttu-id="be935-235">Comment déployer une application hébergée Blazor WebAssembly avec le Identity serveur pour [Azure App service](https://azure.microsoft.com/services/app-service/) avec un domaine personnalisé.</span><span class="sxs-lookup"><span data-stu-id="be935-235">How to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>
* <span data-ttu-id="be935-236">Comment créer et utiliser un certificat TLS pour la communication du protocole HTTPs avec les navigateurs.</span><span class="sxs-lookup"><span data-stu-id="be935-236">How to create and use a TLS certificate for HTTPS protocol communication with browsers.</span></span> <span data-ttu-id="be935-237">Bien que les instructions se concentrent sur l’utilisation du certificat avec un domaine personnalisé, les instructions sont également applicables à l’utilisation d’un domaine Azure Apps par défaut, par exemple `contoso.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="be935-237">Although the guidance focuses on using the certificate with a custom domain, the guidance is equally applicable to using a default Azure Apps domain, for example `contoso.azurewebsites.net`.</span></span>

<span data-ttu-id="be935-238">Pour ce scénario d’hébergement, n’utilisez **pas** le même certificat pour la [ Identity clé de signature de jeton du serveur](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) et la communication sécurisée HTTPS du site avec les navigateurs :</span><span class="sxs-lookup"><span data-stu-id="be935-238">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="be935-239">L’utilisation de différents certificats pour ces deux exigences est une bonne pratique de sécurité, car elle isole les clés privées pour chaque objectif.</span><span class="sxs-lookup"><span data-stu-id="be935-239">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="be935-240">Les certificats TLS pour la communication avec les navigateurs sont gérés indépendamment sans affecter Identity la signature des jetons du serveur.</span><span class="sxs-lookup"><span data-stu-id="be935-240">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="be935-241">Lorsque [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) fournit un certificat à une application App service pour une liaison de domaine personnalisée, Identity le serveur ne peut pas obtenir le même certificat auprès d’Azure Key Vault pour la signature de jetons.</span><span class="sxs-lookup"><span data-stu-id="be935-241">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="be935-242">Bien qu’il Identity soit possible de configurer le serveur pour qu’il utilise le même certificat TLS à partir d’un chemin d’accès physique, il est déconseillé de placer des certificats de sécurité dans le contrôle de code source **et de les éviter dans la plupart des scénarios**.</span><span class="sxs-lookup"><span data-stu-id="be935-242">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="be935-243">Dans les instructions suivantes, un certificat auto-signé est créé dans Azure Key Vault uniquement pour la Identity signature de jeton de serveur.</span><span class="sxs-lookup"><span data-stu-id="be935-243">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="be935-244">La Identity configuration du serveur utilise le certificat de coffre de clés via le magasin de certificats de l’application `My`  >  `CurrentUser` .</span><span class="sxs-lookup"><span data-stu-id="be935-244">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="be935-245">D’autres certificats utilisés pour le trafic HTTPs avec des domaines personnalisés sont créés et configurés séparément du Identity certificat de signature du serveur.</span><span class="sxs-lookup"><span data-stu-id="be935-245">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="be935-246">Pour configurer une application, Azure App Service et Azure Key Vault pour héberger avec un domaine personnalisé et HTTPs :</span><span class="sxs-lookup"><span data-stu-id="be935-246">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="be935-247">Créez un [plan de App service](/azure/app-service/overview-hosting-plans) avec un niveau de plan `Basic B1` supérieur ou égal à.</span><span class="sxs-lookup"><span data-stu-id="be935-247">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="be935-248">App Service nécessite un `Basic B1` niveau de service ou supérieur pour utiliser des domaines personnalisés.</span><span class="sxs-lookup"><span data-stu-id="be935-248">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="be935-249">Créez un certificat PFX pour la communication sécurisée du navigateur du site (protocole HTTPs) avec un nom commun du nom de domaine complet (FQDN) du site que votre organisation contrôle (par exemple, `www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="be935-249">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="be935-250">Créez le certificat avec :</span><span class="sxs-lookup"><span data-stu-id="be935-250">Create the certificate with:</span></span>
   * <span data-ttu-id="be935-251">Utilisations de la clé</span><span class="sxs-lookup"><span data-stu-id="be935-251">Key uses</span></span>
     * <span data-ttu-id="be935-252">Validation de signature numérique ( `digitalSignature` )</span><span class="sxs-lookup"><span data-stu-id="be935-252">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="be935-253">Chiffrement de la clé ( `keyEncipherment` )</span><span class="sxs-lookup"><span data-stu-id="be935-253">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="be935-254">Utilisations de la clé améliorée/étendue</span><span class="sxs-lookup"><span data-stu-id="be935-254">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="be935-255">Authentification du client (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="be935-255">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="be935-256">Authentification du serveur (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="be935-256">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="be935-257">Pour créer le certificat, utilisez l’une des approches suivantes ou tout autre outil approprié ou service en ligne :</span><span class="sxs-lookup"><span data-stu-id="be935-257">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="be935-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="be935-258">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="be935-259">MakeCert sur Windows</span><span class="sxs-lookup"><span data-stu-id="be935-259">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="be935-260">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="be935-260">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="be935-261">Notez le mot de passe, qui est utilisé ultérieurement pour importer le certificat dans Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="be935-261">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="be935-262">Pour plus d’informations sur les certificats Azure Key Vault, consultez [Azure Key Vault : certificats](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="be935-262">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="be935-263">Créez un Azure Key Vault ou utilisez un coffre de clés existant dans votre abonnement Azure.</span><span class="sxs-lookup"><span data-stu-id="be935-263">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="be935-264">Dans la zone **certificats** du coffre de clés, importez le certificat de site pfx.</span><span class="sxs-lookup"><span data-stu-id="be935-264">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="be935-265">Notez l’empreinte numérique du certificat, qui est utilisée ultérieurement dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-265">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="be935-266">Dans Azure Key Vault, générez un nouveau certificat auto-signé pour la Identity signature de jetons du serveur.</span><span class="sxs-lookup"><span data-stu-id="be935-266">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="be935-267">Attribuez un **nom** et un **objet** au certificat.</span><span class="sxs-lookup"><span data-stu-id="be935-267">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="be935-268">Le **sujet** est spécifié en tant que `CN={COMMON NAME}` , où l' `{COMMON NAME}` espace réservé est le nom commun du certificat.</span><span class="sxs-lookup"><span data-stu-id="be935-268">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="be935-269">Le nom commun peut être n’importe quelle chaîne alphanumérique.</span><span class="sxs-lookup"><span data-stu-id="be935-269">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="be935-270">Par exemple, `CN=IdentityServerSigning` est un **objet** de certificat valide.</span><span class="sxs-lookup"><span data-stu-id="be935-270">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="be935-271">Utilisez les paramètres de **configuration de stratégie avancée** par défaut.</span><span class="sxs-lookup"><span data-stu-id="be935-271">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="be935-272">Notez l’empreinte numérique du certificat, qui est utilisée ultérieurement dans la configuration de l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-272">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="be935-273">Accédez à Azure App Service dans le Portail Azure et créez une nouvelle App Service avec la configuration suivante :</span><span class="sxs-lookup"><span data-stu-id="be935-273">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="be935-274">**Publication** définie sur `Code` .</span><span class="sxs-lookup"><span data-stu-id="be935-274">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="be935-275">**Pile d’exécution** définie sur le runtime de l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-275">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="be935-276">Pour la **référence SKU et la taille**, vérifiez que le niveau de App service est `Basic B1` ou supérieur.</span><span class="sxs-lookup"><span data-stu-id="be935-276">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="be935-277">App Service nécessite un `Basic B1` niveau de service ou supérieur pour utiliser des domaines personnalisés.</span><span class="sxs-lookup"><span data-stu-id="be935-277">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="be935-278">Une fois que Azure a créé le App Service, ouvrez la **configuration** de l’application et ajoutez un nouveau paramètre d’application en spécifiant les empreintes numériques de certificat enregistrées précédemment.</span><span class="sxs-lookup"><span data-stu-id="be935-278">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="be935-279">La clé du paramètre d’application est `WEBSITE_LOAD_CERTIFICATES` .</span><span class="sxs-lookup"><span data-stu-id="be935-279">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="be935-280">Séparez les empreintes numériques du certificat dans la valeur du paramètre de l’application par une virgule, comme le montre l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="be935-280">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="be935-281">Clé :`WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="be935-281">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="be935-282">Valeur: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="be935-282">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="be935-283">Dans la Portail Azure, l’enregistrement des paramètres de l’application est un processus en deux étapes : enregistrer le `WEBSITE_LOAD_CERTIFICATES` paramètre clé-valeur, puis sélectionner le bouton **Enregistrer** en haut du panneau.</span><span class="sxs-lookup"><span data-stu-id="be935-283">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="be935-284">Sélectionnez les **paramètres TLS/SSL** de l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-284">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="be935-285">Sélectionnez **certificats de clé privée (. pfx)**.</span><span class="sxs-lookup"><span data-stu-id="be935-285">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="be935-286">Utilisez le processus d' **importation Key Vault certificat** à deux reprises pour importer le certificat du site pour la communication HTTPS et le certificat de signature de jetons de serveur auto-signé du site Identity .</span><span class="sxs-lookup"><span data-stu-id="be935-286">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="be935-287">Accédez au panneau **domaines personnalisés** .</span><span class="sxs-lookup"><span data-stu-id="be935-287">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="be935-288">Sur le site Web de votre bureau d’enregistrement de domaines, utilisez l' **adresse IP** et l' **ID de vérification du domaine personnalisé** pour configurer le domaine.</span><span class="sxs-lookup"><span data-stu-id="be935-288">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="be935-289">Une configuration de domaine classique comprend les éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="be935-289">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="be935-290">Un **enregistrement a** avec un **hôte** `@` et une valeur d’adresse IP de la portail Azure.</span><span class="sxs-lookup"><span data-stu-id="be935-290">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="be935-291">Un **enregistrement txt** avec un **hôte** de `asuid` et la valeur de l’ID de vérification généré par Azure et fourni par le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="be935-291">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="be935-292">Veillez à enregistrer correctement les modifications sur le site Web de votre bureau d’enregistrement de domaines.</span><span class="sxs-lookup"><span data-stu-id="be935-292">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="be935-293">Certains sites Web du Bureau d’enregistrement nécessitent un processus en deux étapes pour enregistrer les enregistrements de domaine : un ou plusieurs enregistrements sont enregistrés individuellement, puis mis à jour l’inscription du domaine avec un bouton distinct.</span><span class="sxs-lookup"><span data-stu-id="be935-293">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="be935-294">Revenez au panneau **domaines personnalisés** dans le portail Azure.</span><span class="sxs-lookup"><span data-stu-id="be935-294">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="be935-295">Sélectionnez **Ajouter un domaine personnalisé**.</span><span class="sxs-lookup"><span data-stu-id="be935-295">Select **Add custom domain**.</span></span> <span data-ttu-id="be935-296">Sélectionnez l’option **A record** .</span><span class="sxs-lookup"><span data-stu-id="be935-296">Select the **A Record** option.</span></span> <span data-ttu-id="be935-297">Fournissez le domaine et sélectionnez **valider**.</span><span class="sxs-lookup"><span data-stu-id="be935-297">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="be935-298">Si les enregistrements de domaine sont corrects et propagés sur Internet, le portail vous permet de sélectionner le bouton **Ajouter un domaine personnalisé** .</span><span class="sxs-lookup"><span data-stu-id="be935-298">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="be935-299">Plusieurs jours peuvent être nécessaires pour que les modifications apportées à l’inscription du domaine se propagent sur les serveurs DNS (Internet Domain Name Server) une fois qu’ils sont traités par votre bureau d’enregistrement de domaines.</span><span class="sxs-lookup"><span data-stu-id="be935-299">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="be935-300">Si les enregistrements de domaine ne sont pas mis à jour dans les trois jours ouvrables, vérifiez que les enregistrements sont correctement définis avec le Bureau d’enregistrement de domaines et contactez le support technique.</span><span class="sxs-lookup"><span data-stu-id="be935-300">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="be935-301">Dans le panneau **domaines personnalisés** , l' **État SSL** pour le domaine est marqué `Not Secure` .</span><span class="sxs-lookup"><span data-stu-id="be935-301">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="be935-302">Sélectionnez le lien **Ajouter une liaison** .</span><span class="sxs-lookup"><span data-stu-id="be935-302">Select the **Add binding** link.</span></span> <span data-ttu-id="be935-303">Sélectionnez le certificat HTTPs du site dans le coffre de clés pour la liaison de domaine personnalisée.</span><span class="sxs-lookup"><span data-stu-id="be935-303">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="be935-304">Dans Visual Studio, ouvrez le fichier de paramètres d’application du projet *serveur* ( `appsettings.json` ou `appsettings.Production.json` ).</span><span class="sxs-lookup"><span data-stu-id="be935-304">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="be935-305">Dans la Identity configuration du serveur, ajoutez la `Key` section suivante.</span><span class="sxs-lookup"><span data-stu-id="be935-305">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="be935-306">Spécifiez le **sujet** du certificat auto-signé pour la `Name` clé.</span><span class="sxs-lookup"><span data-stu-id="be935-306">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="be935-307">Dans l’exemple suivant, le nom commun du certificat affecté dans le coffre de clés est `IdentityServerSigning` , qui donne l' **objet** `CN=IdentityServerSigning` :</span><span class="sxs-lookup"><span data-stu-id="be935-307">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="be935-308">Dans Visual Studio, créez un Azure App Service [profil de publication](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) pour le projet *serveur* .</span><span class="sxs-lookup"><span data-stu-id="be935-308">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="be935-309">Dans la barre de menus, sélectionnez : **générer**  >  **publier**  >  **de nouveaux**  >    >  **Azure App service** Azure (Windows ou Linux).</span><span class="sxs-lookup"><span data-stu-id="be935-309">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="be935-310">Lorsque Visual Studio est connecté à un abonnement Azure, vous pouvez définir l' **affichage** des ressources Azure par **type de ressource**.</span><span class="sxs-lookup"><span data-stu-id="be935-310">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="be935-311">Naviguez dans la liste des **applications Web** pour rechercher les app service de l’application et sélectionnez-la.</span><span class="sxs-lookup"><span data-stu-id="be935-311">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="be935-312">Sélectionnez **Terminer**.</span><span class="sxs-lookup"><span data-stu-id="be935-312">Select **Finish**.</span></span>
1. <span data-ttu-id="be935-313">Lorsque Visual Studio revient à la fenêtre de **publication** , les dépendances du coffre de clés et du service de base de données SQL Server sont détectées automatiquement.</span><span class="sxs-lookup"><span data-stu-id="be935-313">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="be935-314">Aucune modification de la configuration des paramètres par défaut n’est requise pour le service de coffre de clés.</span><span class="sxs-lookup"><span data-stu-id="be935-314">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="be935-315">À des fins de test, la base de données [SQLite](https://www.sqlite.org/index.html) locale d’une application, qui est configurée par défaut par le Blazor modèle, peut être déployée avec l’application sans configuration supplémentaire.</span><span class="sxs-lookup"><span data-stu-id="be935-315">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="be935-316">La configuration d’une base de données différente pour Identity le serveur en production dépasse le cadre de cet article.</span><span class="sxs-lookup"><span data-stu-id="be935-316">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="be935-317">Pour plus d’informations, consultez les ressources de base de données dans les ensembles de documentation suivants :</span><span class="sxs-lookup"><span data-stu-id="be935-317">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="be935-318">App Service</span><span class="sxs-lookup"><span data-stu-id="be935-318">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="be935-319">Identity Serveurs</span><span class="sxs-lookup"><span data-stu-id="be935-319">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="be935-320">Sélectionnez le lien **modifier** sous le nom du profil de déploiement en haut de la fenêtre.</span><span class="sxs-lookup"><span data-stu-id="be935-320">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="be935-321">Remplacez l’URL de destination par l’URL de domaine personnalisée du site (par exemple, `https://www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="be935-321">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="be935-322">Enregistrez les paramètres.</span><span class="sxs-lookup"><span data-stu-id="be935-322">Save the settings.</span></span>
1. <span data-ttu-id="be935-323">Publiez l’application.</span><span class="sxs-lookup"><span data-stu-id="be935-323">Publish the app.</span></span> <span data-ttu-id="be935-324">Visual Studio ouvre une fenêtre de navigateur et demande le site dans son domaine personnalisé.</span><span class="sxs-lookup"><span data-stu-id="be935-324">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="be935-325">La documentation Azure contient des informations supplémentaires sur l’utilisation des services Azure et des domaines personnalisés avec une liaison TLS dans App Service, y compris des informations sur l’utilisation d’enregistrements CNAMe à la place d’enregistrements A.</span><span class="sxs-lookup"><span data-stu-id="be935-325">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="be935-326">Pour plus d’informations, consultez les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="be935-326">For more information, see the following resources:</span></span>

* [<span data-ttu-id="be935-327">Documentation App Service</span><span class="sxs-lookup"><span data-stu-id="be935-327">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="be935-328">Tutoriel : Mapper un nom DNS personnalisé existant à Azure App Service</span><span class="sxs-lookup"><span data-stu-id="be935-328">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="be935-329">Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service</span><span class="sxs-lookup"><span data-stu-id="be935-329">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="be935-330">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="be935-330">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="be935-331">Nous vous recommandons d’utiliser une nouvelle fenêtre de navigateur incognito ou privée pour chaque exécution de test d’application après une modification de l’application, la configuration de l’application ou les services Azure dans la Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="be935-331">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="be935-332">cookieLes s en attente d’une série de tests précédente peuvent entraîner l’échec de l’authentification ou de l’autorisation lors du test du site, même lorsque la configuration du site est correcte.</span><span class="sxs-lookup"><span data-stu-id="be935-332">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="be935-333">Pour plus d’informations sur la configuration de Visual Studio pour ouvrir une nouvelle fenêtre de navigateur dans incognito ou privée pour chaque série de tests, consultez la section [ Cookie s et données de site](#cookies-and-site-data) .</span><span class="sxs-lookup"><span data-stu-id="be935-333">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="be935-334">Lorsque App Service configuration est modifiée dans le Portail Azure, les mises à jour prennent généralement effet rapidement mais ne sont pas instantanées.</span><span class="sxs-lookup"><span data-stu-id="be935-334">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="be935-335">Parfois, vous devez attendre un peu de temps pour qu’un App Service redémarre afin que la modification de la configuration prenne effet.</span><span class="sxs-lookup"><span data-stu-id="be935-335">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="be935-336">Si vous résolvez un problème de chargement de certificat, exécutez la commande suivante dans une interface de commande PowerShell Portail Azure [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) .</span><span class="sxs-lookup"><span data-stu-id="be935-336">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="be935-337">La commande fournit une liste de certificats auxquels l’application peut accéder à partir du `My`  >  `CurrentUser` magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="be935-337">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="be935-338">La sortie comprend les objets de certificat et les empreintes numériques utiles lors du débogage d’une application :</span><span class="sxs-lookup"><span data-stu-id="be935-338">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="be935-339">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="be935-339">Additional resources</span></span>

* [<span data-ttu-id="be935-340">Déploiement sur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="be935-340">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="be935-341">Importer un certificat à partir d’Key Vault (documentation Azure)</span><span class="sxs-lookup"><span data-stu-id="be935-341">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="be935-342">Demandes d’API Web non authentifiées ou non autorisées dans une application avec un client par défaut sécurisé</span><span class="sxs-lookup"><span data-stu-id="be935-342">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="be935-343"><xref:host-and-deploy/proxy-load-balancer>: Fournit des conseils sur :</span><span class="sxs-lookup"><span data-stu-id="be935-343"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="be935-344">Utilisation de l’intergiciel d’en-têtes transférés pour conserver les informations de schéma HTTPs entre les serveurs proxy et les réseaux internes.</span><span class="sxs-lookup"><span data-stu-id="be935-344">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="be935-345">Scénarios et cas d’utilisation supplémentaires, y compris la configuration manuelle du schéma, les modifications du chemin des demandes pour le routage des demandes correct et le transfert du modèle de demande pour les proxys inversés Linux et non-IIS.</span><span class="sxs-lookup"><span data-stu-id="be935-345">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
