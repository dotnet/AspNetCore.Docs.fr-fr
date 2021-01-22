---
title: Présentation de l’authentification pour les applications à page unique sur ASP.NET Core
author: javiercn
description: Utilisez Identity avec une application à page unique hébergée dans une application ASP.net core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 5a6c160ebdda3ec600980aa839770f4f22a9c2fc
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658662"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="7b1ea-103">Authentification et autorisation pour SPAs</span><span class="sxs-lookup"><span data-stu-id="7b1ea-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="7b1ea-104">Les modèles ASP.NET Core 3,1 et versions ultérieures offrent une authentification dans les applications à page unique (SPAs) à l’aide de la prise en charge de l’autorisation de l’API.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="7b1ea-105">ASP.NET Core Identitypour l’authentification et le stockage des utilisateurs est associé au [ Identity serveur](https://identityserver.io/) pour l’implémentation de OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="7b1ea-106">Un paramètre d’authentification a été ajouté aux modèles de projet **angulaire** et **REACT** qui est similaire au paramètre d’authentification dans les modèles de projet **application Web (Model-View-Controller)** et **application Web** ( Razor pages).</span><span class="sxs-lookup"><span data-stu-id="7b1ea-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="7b1ea-107">Les valeurs de paramètre autorisées sont **None** et **Individual**.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="7b1ea-108">Le modèle de projet **React.js et Redux** ne prend pas en charge le paramètre d’authentification pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="7b1ea-109">Créer une application avec prise en charge des autorisations d’API</span><span class="sxs-lookup"><span data-stu-id="7b1ea-109">Create an app with API authorization support</span></span>

<span data-ttu-id="7b1ea-110">L’authentification et l’autorisation de l’utilisateur peuvent être utilisées avec les deux types d’angle et de réaction.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="7b1ea-111">Ouvrez une interface de commande, puis exécutez la commande suivante :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="7b1ea-112">**Angulaire**:</span><span class="sxs-lookup"><span data-stu-id="7b1ea-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="7b1ea-113">**Réaction**:</span><span class="sxs-lookup"><span data-stu-id="7b1ea-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="7b1ea-114">La commande précédente crée une application ASP.NET Core avec un répertoire *ClientApp* contenant le Spa.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="7b1ea-115">Description générale des composants ASP.NET Core de l’application</span><span class="sxs-lookup"><span data-stu-id="7b1ea-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="7b1ea-116">Les sections suivantes décrivent les ajouts au projet lorsque la prise en charge de l’authentification est incluse :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="7b1ea-117">Classe de démarrage</span><span class="sxs-lookup"><span data-stu-id="7b1ea-117">Startup class</span></span>

<span data-ttu-id="7b1ea-118">Les exemples de code suivants reposent sur [Microsoft. AspNetCore. ApiAuthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) Package NuGet du serveur.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="7b1ea-119">Les exemples configurent l’authentification et l’autorisation des API à l’aide des <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> méthodes d’extension et.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="7b1ea-120">Les projets qui utilisent les modèles de projet REACT ou SPA angulaire avec l’authentification incluent une référence à ce package.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="7b1ea-121">La `Startup` classe comporte les ajouts suivants :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="7b1ea-122">À l’intérieur de la `Startup.ConfigureServices` méthode :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="7b1ea-123">Identity avec l’interface utilisateur par défaut :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="7b1ea-124">IdentityServeur avec une `AddApiAuthorization` méthode d’assistance supplémentaire qui définit des conventions de ASP.net Core par défaut sur le Identity serveur :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="7b1ea-125">Authentification avec une `AddIdentityServerJwt` méthode d’assistance supplémentaire qui configure l’application pour valider les jetons JWT générés par le Identity serveur :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="7b1ea-126">À l’intérieur de la `Startup.Configure` méthode :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="7b1ea-127">Intergiciel d’authentification responsable de la validation des informations d’identification de la demande et de la définition de l’utilisateur dans le contexte de la requête :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="7b1ea-128">L' Identity intergiciel de serveur qui expose les points de terminaison OpenID Connect :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="7b1ea-129">Azure App Service sur Linux</span><span class="sxs-lookup"><span data-stu-id="7b1ea-129">Azure App Service on Linux</span></span>

<span data-ttu-id="7b1ea-130">Pour les déploiements de Azure App Service sur Linux, spécifiez l’émetteur de manière explicite dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-130">For Azure App Service deployments on Linux, specify the issuer explicitly in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme, 
    options =>
    {
        options.Authority = "{AUTHORITY}";
    });
```

<span data-ttu-id="7b1ea-131">Dans le code précédent, l' `{AUTHORITY}` espace réservé est le <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> à utiliser pour effectuer des appels OpenID Connect.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-131">In the preceding code, the `{AUTHORITY}` placeholder is the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> to use when making OpenID Connect calls.</span></span>

<span data-ttu-id="7b1ea-132">Exemple :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-132">Example:</span></span>

```csharp
options.Authority = "https://contoso-service.azurewebsites.net";
```

### <a name="addapiauthorization"></a><span data-ttu-id="7b1ea-133">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="7b1ea-133">AddApiAuthorization</span></span>

<span data-ttu-id="7b1ea-134">Cette méthode d’assistance configure le Identity serveur pour qu’il utilise notre configuration prise en charge.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-134">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="7b1ea-135">IdentityLe serveur est un Framework puissant et extensible pour gérer les problèmes de sécurité des applications.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-135">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="7b1ea-136">En même temps, cela expose une complexité inutile pour les scénarios les plus courants.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-136">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="7b1ea-137">Par conséquent, un ensemble de conventions et d’options de configuration qui vous sont fournies est considéré comme un bon point de départ.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-137">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="7b1ea-138">Une fois vos besoins d’authentification modifiés, toute la puissance du Identity serveur est toujours disponible pour personnaliser l’authentification en fonction de vos besoins.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-138">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="7b1ea-139">Ajouter Identity ServerJwt</span><span class="sxs-lookup"><span data-stu-id="7b1ea-139">AddIdentityServerJwt</span></span>

<span data-ttu-id="7b1ea-140">Cette méthode d’assistance configure un modèle de stratégie pour l’application en tant que gestionnaire d’authentification par défaut.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-140">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="7b1ea-141">La stratégie est configurée pour permettre à de Identity gérer toutes les demandes routées vers n’importe quel sous-chemin dans l' Identity espace d’URL « / Identity ».</span><span class="sxs-lookup"><span data-stu-id="7b1ea-141">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="7b1ea-142">Le `JwtBearerHandler` gère toutes les autres requêtes.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-142">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="7b1ea-143">En outre, cette méthode enregistre une `<<ApplicationName>>API` ressource API avec Identity le serveur avec une étendue par défaut `<<ApplicationName>>API` et configure l’intergiciel de jeton de porteur JWT pour valider les jetons émis par Identity le serveur pour l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-143">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="7b1ea-144">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="7b1ea-144">WeatherForecastController</span></span>

<span data-ttu-id="7b1ea-145">Dans le fichier *Controllers\WeatherForecastController.cs* , notez l' `[Authorize]` attribut appliqué à la classe qui indique que l’utilisateur doit être autorisé en fonction de la stratégie par défaut pour accéder à la ressource.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-145">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="7b1ea-146">La stratégie d’autorisation par défaut doit être configurée pour utiliser le schéma d’authentification par défaut, qui est configuré par `AddIdentityServerJwt` le modèle de stratégie mentionné plus haut, ce qui fait du `JwtBearerHandler` configuré par une telle méthode d’assistance le gestionnaire par défaut pour les demandes à l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-146">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="7b1ea-147">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="7b1ea-147">ApplicationDbContext</span></span>

<span data-ttu-id="7b1ea-148">Dans le fichier *Data\ApplicationDbContext.cs* , Notez que le même `DbContext` est utilisé dans, à Identity l’exception qu’il étend `ApiAuthorizationDbContext` (une classe plus dérivée de `IdentityDbContext` ) pour inclure le schéma pour le Identity serveur.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-148">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="7b1ea-149">Pour obtenir le contrôle total du schéma de base de données, héritez de l’une des Identity `DbContext` classes disponibles et configurez le contexte pour inclure le Identity schéma en appelant `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` sur la `OnModelCreating` méthode.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-149">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="7b1ea-150">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="7b1ea-150">OidcConfigurationController</span></span>

<span data-ttu-id="7b1ea-151">Dans le fichier *Controllers\OidcConfigurationController.cs* , notez le point de terminaison configuré pour servir les paramètres OIDC que le client doit utiliser.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-151">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### appsettings.json

<span data-ttu-id="7b1ea-152">Dans le *appsettings.json* fichier de la racine du projet, une nouvelle `IdentityServer` section décrit la liste des clients configurés.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-152">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="7b1ea-153">Dans l’exemple suivant, il existe un seul client.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-153">In the following example, there's a single client.</span></span> <span data-ttu-id="7b1ea-154">Le nom du client correspond au nom de l’application et est mappé par Convention au `ClientId` paramètre OAuth.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="7b1ea-155">Le profil indique le type d’application en cours de configuration.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="7b1ea-156">Il est utilisé en interne pour générer des conventions qui simplifient le processus de configuration du serveur.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-156">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="7b1ea-157">Plusieurs profils sont disponibles, comme expliqué dans la section [profils d’application](#application-profiles) .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-157">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="7b1ea-158">appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="7b1ea-158">appsettings.Development.json</span></span>

<span data-ttu-id="7b1ea-159">Dans le *appsettings.Development.js* fichier de la racine du projet, il existe une `IdentityServer` section qui décrit la clé utilisée pour signer les jetons.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-159">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="7b1ea-160">Lors du déploiement en production, une clé doit être approvisionnée et déployée parallèlement à l’application, comme expliqué dans la section [déploiement en production](#deploy-to-production) .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-160">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="7b1ea-161">Description générale de l’application angulaire</span><span class="sxs-lookup"><span data-stu-id="7b1ea-161">General description of the Angular app</span></span>

<span data-ttu-id="7b1ea-162">L’authentification et la prise en charge de l’autorisation d’API dans le modèle angulaire résident dans son propre module angulaire dans le répertoire *ClientApp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-162">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="7b1ea-163">Le module est composé des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-163">The module is composed of the following elements:</span></span>

* <span data-ttu-id="7b1ea-164">3 composants :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-164">3 components:</span></span>
  * <span data-ttu-id="7b1ea-165">*login. Component. TS*: gère le déroulement de la connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-165">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="7b1ea-166">*logout. Component. TS*: gère le déroulement de la déconnexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-166">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="7b1ea-167">*login-menu. Component. TS*: un widget qui affiche l’un des ensembles de liens suivants :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-167">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="7b1ea-168">Gestion des profils utilisateur et déconnexion des liens lorsque l’utilisateur est authentifié.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-168">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="7b1ea-169">Enregistrement et connexion des liens lorsque l’utilisateur n’est pas authentifié.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-169">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="7b1ea-170">Une protection de routage `AuthorizeGuard` qui peut être ajoutée aux itinéraires et requiert qu’un utilisateur soit authentifié avant de visiter l’itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-170">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="7b1ea-171">Intercepteur HTTP `AuthorizeInterceptor` qui associe le jeton d’accès aux requêtes http sortantes ciblant l’API lorsque l’utilisateur est authentifié.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-171">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="7b1ea-172">Service `AuthorizeService` qui gère les détails de niveau inférieur du processus d’authentification et expose des informations sur l’utilisateur authentifié au reste de l’application à des fins de consommation.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-172">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="7b1ea-173">Module angulaire qui définit les itinéraires associés aux parties d’authentification de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-173">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="7b1ea-174">Il expose le composant de menu de connexion, l’intercepteur, la protection et le service à consommer à partir du reste de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-174">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="7b1ea-175">Description générale de l’application REACT</span><span class="sxs-lookup"><span data-stu-id="7b1ea-175">General description of the React app</span></span>

<span data-ttu-id="7b1ea-176">La prise en charge de l’authentification et de l’autorisation d’API dans le modèle REACT réside dans le répertoire *ClientApp\src\components\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-176">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="7b1ea-177">Il se compose des éléments suivants :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-177">It's composed of the following elements:</span></span>

* <span data-ttu-id="7b1ea-178">4 composants :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-178">4 components:</span></span>
  * <span data-ttu-id="7b1ea-179">*Login.js*: gère le déroulement de la connexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-179">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="7b1ea-180">*Logout.js*: gère le déroulement de la déconnexion de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-180">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="7b1ea-181">*LoginMenu.js*: un widget qui affiche l’un des ensembles de liens suivants :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-181">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="7b1ea-182">Gestion des profils utilisateur et déconnexion des liens lorsque l’utilisateur est authentifié.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-182">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="7b1ea-183">Enregistrement et connexion des liens lorsque l’utilisateur n’est pas authentifié.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-183">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="7b1ea-184">*AuthorizeRoute.js*: composant de routage qui requiert l’authentification d’un utilisateur avant le rendu du composant indiqué dans le `Component` paramètre.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-184">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="7b1ea-185">Instance exportée `authService` de la classe `AuthorizeService` qui gère les détails de niveau inférieur du processus d’authentification et expose des informations sur l’utilisateur authentifié au reste de l’application à des fins de consommation.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-185">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="7b1ea-186">Maintenant que vous avez vu les principaux composants de la solution, vous pouvez examiner de manière plus détaillée les scénarios individuels de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-186">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="7b1ea-187">Exiger une autorisation sur une nouvelle API</span><span class="sxs-lookup"><span data-stu-id="7b1ea-187">Require authorization on a new API</span></span>

<span data-ttu-id="7b1ea-188">Par défaut, le système est configuré pour demander facilement une autorisation pour les nouvelles API.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-188">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="7b1ea-189">Pour ce faire, créez un nouveau contrôleur et ajoutez l' `[Authorize]` attribut à la classe de contrôleur ou à n’importe quelle action dans le contrôleur.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-189">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="7b1ea-190">Personnaliser le gestionnaire d’authentification d’API</span><span class="sxs-lookup"><span data-stu-id="7b1ea-190">Customize the API authentication handler</span></span>

<span data-ttu-id="7b1ea-191">Pour personnaliser la configuration du gestionnaire JWT de l’API, configurez son <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-191">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="7b1ea-192">Le gestionnaire JWT de l’API déclenche des événements qui permettent de contrôler le processus d’authentification à l’aide de `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-192">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="7b1ea-193">Pour assurer la prise en charge de l’autorisation d’API, `AddIdentityServerJwt` inscrit ses propres gestionnaires d’événements.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-193">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="7b1ea-194">Pour personnaliser la gestion d’un événement, encapsulez le gestionnaire d’événements existant avec une logique supplémentaire, le cas échéant.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-194">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="7b1ea-195">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-195">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="7b1ea-196">Dans le code précédent, le `OnTokenValidated` Gestionnaire d’événements est remplacé par une implémentation personnalisée.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-196">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="7b1ea-197">Cette implémentation :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-197">This implementation:</span></span>

1. <span data-ttu-id="7b1ea-198">Appelle l’implémentation d’origine fournie par la prise en charge de l’autorisation d’API.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-198">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="7b1ea-199">Exécutez sa propre logique personnalisée.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-199">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="7b1ea-200">Protéger un itinéraire côté client (angulaire)</span><span class="sxs-lookup"><span data-stu-id="7b1ea-200">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="7b1ea-201">La protection d’un itinéraire côté client s’effectue en ajoutant la protection d’autorisation à la liste des gardes à exécuter lors de la configuration d’un itinéraire.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-201">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="7b1ea-202">Par exemple, vous pouvez voir comment l' `fetch-data` itinéraire est configuré dans le module angulaire principal de l’application :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-202">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="7b1ea-203">Il est important de mentionner que la protection d’un itinéraire ne protège pas le point de terminaison réel (qui requiert toujours un `[Authorize]` attribut qui lui est appliqué), mais qu’il empêche uniquement l’utilisateur de naviguer vers l’itinéraire donné côté client lorsqu’il n’est pas authentifié.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-203">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="7b1ea-204">Authentifier les demandes d’API (angulaires)</span><span class="sxs-lookup"><span data-stu-id="7b1ea-204">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="7b1ea-205">L’authentification des demandes auprès des API hébergées parallèlement à l’application s’effectue automatiquement par le biais de l’intercepteur client HTTP défini par l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-205">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="7b1ea-206">Protéger un itinéraire côté client (REACT)</span><span class="sxs-lookup"><span data-stu-id="7b1ea-206">Protect a client-side route (React)</span></span>

<span data-ttu-id="7b1ea-207">Protégez un itinéraire côté client à l’aide du `AuthorizeRoute` composant au lieu du `Route` composant simple.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-207">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="7b1ea-208">Par exemple, notez la configuration de l' `fetch-data` itinéraire dans le `App` composant :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-208">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="7b1ea-209">Protection d’un itinéraire :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-209">Protecting a route:</span></span>

* <span data-ttu-id="7b1ea-210">Ne protège pas le point de terminaison réel (qui requiert toujours un `[Authorize]` attribut qui lui est appliqué).</span><span class="sxs-lookup"><span data-stu-id="7b1ea-210">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="7b1ea-211">Empêche uniquement l’utilisateur de naviguer vers l’itinéraire donné côté client lorsqu’il n’est pas authentifié.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-211">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="7b1ea-212">Authentifier les demandes d’API (REACT)</span><span class="sxs-lookup"><span data-stu-id="7b1ea-212">Authenticate API requests (React)</span></span>

<span data-ttu-id="7b1ea-213">L’authentification des demandes avec REACT est effectuée en important `authService` d’abord l’instance à partir du `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-213">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="7b1ea-214">Le jeton d’accès est récupéré à partir de `authService` et est attaché à la demande, comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-214">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="7b1ea-215">Dans les composants REACT, ce travail est généralement effectué dans la `componentDidMount` méthode Lifecycle ou en tant que résultat de certaines interactions de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-215">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="7b1ea-216">Importer le authService dans votre composant</span><span class="sxs-lookup"><span data-stu-id="7b1ea-216">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="7b1ea-217">Récupérer et attacher le jeton d’accès à la réponse</span><span class="sxs-lookup"><span data-stu-id="7b1ea-217">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="7b1ea-218">Déployer en production</span><span class="sxs-lookup"><span data-stu-id="7b1ea-218">Deploy to production</span></span>

<span data-ttu-id="7b1ea-219">Pour déployer l’application en production, vous devez configurer les ressources suivantes :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-219">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="7b1ea-220">Une base de données pour stocker les Identity comptes d’utilisateur et le Identity serveur.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-220">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="7b1ea-221">Certificat de production à utiliser pour la signature des jetons.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-221">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="7b1ea-222">Il n’existe aucune exigence spécifique pour ce certificat. Il peut s’agir d’un certificat auto-signé ou d’un certificat approvisionné par le biais d’une autorité de certification.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-222">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="7b1ea-223">Il peut être généré à l’aide d’outils standard tels que PowerShell ou OpenSSL.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-223">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="7b1ea-224">Il peut être installé dans le magasin de certificats sur les ordinateurs cibles ou déployé en tant que fichier *. pfx* avec un mot de passe fort.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-224">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="7b1ea-225">Exemple : déployer sur un fournisseur d’hébergement Web non Azure</span><span class="sxs-lookup"><span data-stu-id="7b1ea-225">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="7b1ea-226">Dans le volet d’hébergement Web, créez ou chargez votre certificat.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-226">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="7b1ea-227">Ensuite, dans le fichier de l’application *appsettings.json* , modifiez la `IdentityServer` section pour inclure les détails de la clé.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-227">Then in the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details.</span></span> <span data-ttu-id="7b1ea-228">Par exemple :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-228">For example:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="7b1ea-229">Dans l’exemple précédent :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-229">In the preceding example:</span></span>

* <span data-ttu-id="7b1ea-230">`StoreName` représente le nom du magasin de certificats dans lequel le certificat est stocké.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-230">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="7b1ea-231">Dans ce cas, il pointe vers le magasin d’hébergement Web.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-231">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="7b1ea-232">`StoreLocation` représente l’emplacement à partir duquel le certificat doit être chargé ( `CurrentUser` dans le cas présent).</span><span class="sxs-lookup"><span data-stu-id="7b1ea-232">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="7b1ea-233">`Name` correspond au sujet distinctif du certificat.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-233">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="7b1ea-234">Exemple : déployer sur Azure App Service</span><span class="sxs-lookup"><span data-stu-id="7b1ea-234">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="7b1ea-235">Cette section décrit le déploiement de l’application sur Azure App Service à l’aide d’un certificat stocké dans le magasin de certificats.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-235">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="7b1ea-236">Pour modifier l’application afin de charger un certificat à partir du magasin de certificats, un plan de service de niveau standard ou mieux est requis lorsque vous configurez l’application dans le Portail Azure à une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-236">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="7b1ea-237">Dans le fichier de l’application *appsettings.json* , modifiez la `IdentityServer` section pour inclure les détails de la clé :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-237">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="7b1ea-238">Le nom du magasin représente le nom du magasin de certificats dans lequel le certificat est stocké.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-238">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="7b1ea-239">Dans ce cas, il pointe vers le magasin de l’utilisateur personnel.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-239">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="7b1ea-240">L’emplacement du magasin représente l’emplacement de chargement du certificat à partir de ( `CurrentUser` ou `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="7b1ea-240">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="7b1ea-241">La propriété Name du certificat correspond au sujet distinctif du certificat.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-241">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="7b1ea-242">Pour effectuer un déploiement sur Azure App Service, suivez les étapes de la section [déployer l’application sur Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), qui explique comment créer les ressources Azure nécessaires et comment déployer l’application en production.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-242">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="7b1ea-243">Après avoir effectué les instructions précédentes, l’application est déployée dans Azure, mais n’est pas encore fonctionnelle.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-243">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="7b1ea-244">Le certificat utilisé par l’application doit être configuré dans la Portail Azure.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-244">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="7b1ea-245">Localisez l’empreinte numérique du certificat et suivez les étapes décrites dans [charger vos certificats](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="7b1ea-245">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="7b1ea-246">Bien que ces étapes mentionnent SSL, il existe une section **Private Certificates** dans le portail Azure où vous pouvez télécharger le certificat approvisionné à utiliser avec l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-246">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="7b1ea-247">Après avoir configuré l’application et les paramètres de l’application dans la Portail Azure, redémarrez l’application dans le portail.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-247">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="7b1ea-248">Autres options de configuration</span><span class="sxs-lookup"><span data-stu-id="7b1ea-248">Other configuration options</span></span>

<span data-ttu-id="7b1ea-249">La prise en charge de l’autorisation d’API s’appuie sur le Identity serveur avec un ensemble de conventions, de valeurs par défaut et d’améliorations pour simplifier l’expérience de la création de la base de code.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-249">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="7b1ea-250">Inutile de préciser que la pleine puissance du Identity serveur est disponible en arrière-plan si les intégrations de ASP.net Core ne couvrent pas votre scénario.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-250">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="7b1ea-251">La prise en charge ASP.NET Core est axée sur les applications « internes », où toutes les applications sont créées et déployées par notre organisation.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-251">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="7b1ea-252">Par conséquent, le support n’est pas proposé pour les éléments tels que le consentement ou la Fédération.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-252">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="7b1ea-253">Pour ces scénarios, utilisez Identity le serveur et suivez leur documentation.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-253">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="7b1ea-254">Profils d’application</span><span class="sxs-lookup"><span data-stu-id="7b1ea-254">Application profiles</span></span>

<span data-ttu-id="7b1ea-255">Les profils d’application sont des configurations prédéfinies pour les applications qui définissent davantage leurs paramètres.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-255">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="7b1ea-256">À ce stade, les profils suivants sont pris en charge :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-256">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="7b1ea-257">`IdentityServerSPA`: Représente un SPA hébergé Identity en même temps que le serveur en tant qu’unité unique.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-257">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="7b1ea-258">La `redirect_uri` valeur par défaut est `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-258">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="7b1ea-259">La `post_logout_redirect_uri` valeur par défaut est `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-259">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="7b1ea-260">L’ensemble d’étendues comprend `openid` , `profile` et toutes les étendues définies pour les API dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="7b1ea-261">L’ensemble des types de réponses OIDC autorisés est `id_token token` ou chacun d’eux individuellement ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="7b1ea-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="7b1ea-262">Le mode de réponse autorisé est `fragment` .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="7b1ea-263">`SPA`: Représente un SPA qui n’est pas hébergé avec le Identity serveur.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-263">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="7b1ea-264">L’ensemble d’étendues comprend `openid` , `profile` et toutes les étendues définies pour les API dans l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-264">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="7b1ea-265">L’ensemble des types de réponses OIDC autorisés est `id_token token` ou chacun d’eux individuellement ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="7b1ea-265">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="7b1ea-266">Le mode de réponse autorisé est `fragment` .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-266">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="7b1ea-267">`IdentityServerJwt`: Représente une API hébergée en même temps que le Identity serveur.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-267">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="7b1ea-268">L’application est configurée pour avoir une seule étendue qui correspond par défaut au nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-268">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="7b1ea-269">`API`: Représente une API qui n’est pas hébergée sur le Identity serveur.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-269">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="7b1ea-270">L’application est configurée pour avoir une seule étendue qui correspond par défaut au nom de l’application.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-270">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="7b1ea-271">Configuration via AppSettings</span><span class="sxs-lookup"><span data-stu-id="7b1ea-271">Configuration through AppSettings</span></span>

<span data-ttu-id="7b1ea-272">Configurez les applications par le biais du système de configuration en les ajoutant à la liste de `Clients` ou `Resources` .</span><span class="sxs-lookup"><span data-stu-id="7b1ea-272">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="7b1ea-273">Configurez les `redirect_uri` Propriétés et de chaque client `post_logout_redirect_uri` , comme indiqué dans l’exemple suivant :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-273">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="7b1ea-274">Lors de la configuration des ressources, vous pouvez configurer les étendues de la ressource comme indiqué ci-dessous :</span><span class="sxs-lookup"><span data-stu-id="7b1ea-274">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="7b1ea-275">Configuration par le biais du code</span><span class="sxs-lookup"><span data-stu-id="7b1ea-275">Configuration through code</span></span>

<span data-ttu-id="7b1ea-276">Vous pouvez également configurer les clients et les ressources par le biais du code à l’aide d’une surcharge de `AddApiAuthorization` qui prend une mesure pour configurer les options.</span><span class="sxs-lookup"><span data-stu-id="7b1ea-276">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="7b1ea-277">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="7b1ea-277">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
