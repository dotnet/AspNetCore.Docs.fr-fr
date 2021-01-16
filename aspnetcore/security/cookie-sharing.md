---
title: Partager les authentifications cookie entre les applications ASP.net
author: rick-anderson
description: Découvrez comment partager des authentifications cookie entre ASP.net 4. x et des applications ASP.net core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252992"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="e8ffb-103">Partager les authentifications cookie entre les applications ASP.net</span><span class="sxs-lookup"><span data-stu-id="e8ffb-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="e8ffb-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e8ffb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e8ffb-105">Les sites Web sont souvent constitués d’applications Web individuelles qui fonctionnent ensemble.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="e8ffb-106">Pour fournir une expérience d’authentification unique (SSO), les applications Web d’un site doivent partager les cookie s d’authentification.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="e8ffb-107">Pour prendre en charge ce scénario, la pile de protection des données permet de partager cookie l’authentification Katana et les cookie tickets d’authentification ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="e8ffb-108">Dans les exemples suivants :</span><span class="sxs-lookup"><span data-stu-id="e8ffb-108">In the examples that follow:</span></span>

* <span data-ttu-id="e8ffb-109">Le nom d’authentification cookie est défini sur la valeur courante `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="e8ffb-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="e8ffb-110">`AuthenticationType`Est défini sur `Identity.Application` explicitement ou par défaut.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="e8ffb-111">Un nom d’application commun est utilisé pour permettre au système de protection des données de partager des clés de protection des données ( `SharedCookieApp` ).</span><span class="sxs-lookup"><span data-stu-id="e8ffb-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="e8ffb-112">`Identity.Application` est utilisé comme schéma d’authentification.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="e8ffb-113">Quel que soit le schéma utilisé, il doit être utilisé de manière cohérente *dans et dans* les applications partagées, cookie soit en tant que modèle par défaut, soit en le définissant explicitement.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="e8ffb-114">Le schéma est utilisé lors du chiffrement et du déchiffrement de cookie s, de sorte qu’un schéma cohérent doit être utilisé entre les applications.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="e8ffb-115">Un emplacement de stockage de [clé de protection des données](xref:security/data-protection/implementation/key-management) commun est utilisé.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="e8ffb-116">Dans ASP.NET Core Apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> est utilisé pour définir l’emplacement de stockage de la clé.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="e8ffb-117">Dans .NET Framework Apps, Cookie l’intergiciel d’authentification utilise une implémentation de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="e8ffb-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="e8ffb-118">`DataProtectionProvider` fournit des services de protection des données pour le chiffrement et le déchiffrement des cookie données de charge utile d’authentification.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="e8ffb-119">L' `DataProtectionProvider` instance est isolée du système de protection des données utilisé par d’autres parties de l’application.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="e8ffb-120">[DataProtectionProvider. Create (System. IO. DirectoryInfo, action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepte un <xref:System.IO.DirectoryInfo> pour spécifier l’emplacement du stockage de la clé de protection des données.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="e8ffb-121">`DataProtectionProvider` requiert le package NuGet [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="e8ffb-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="e8ffb-122">Dans ASP.NET Core applications 2. x, référencez le [AspNetCore](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="e8ffb-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="e8ffb-123">Dans .NET Framework applications, ajoutez une référence de package à [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="e8ffb-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="e8ffb-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> définit le nom d’application courant.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="e8ffb-125">Partager des authentifications cookie avec ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="e8ffb-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="e8ffb-126">Lorsque vous utilisez ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="e8ffb-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="e8ffb-127">Les clés de protection des données et le nom de l’application doivent être partagés entre les applications.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="e8ffb-128">Un emplacement de stockage de clés commun est fourni à la <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> méthode dans les exemples suivants.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="e8ffb-129">Utilisez <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> pour configurer un nom d’application partagée commun ( `SharedCookieApp` dans les exemples suivants).</span><span class="sxs-lookup"><span data-stu-id="e8ffb-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="e8ffb-130">Pour plus d'informations, consultez <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="e8ffb-131">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> méthode d’extension pour configurer le service de protection des données pour les cookie .</span><span class="sxs-lookup"><span data-stu-id="e8ffb-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="e8ffb-132">Le type d’authentification par défaut est `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="e8ffb-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="e8ffb-133">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="e8ffb-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="e8ffb-134">**Remarque :** Les instructions précédentes ne fonctionnent pas avec `ITicketStore` ( `CookieAuthenticationOptions.SessionStore` ).</span><span class="sxs-lookup"><span data-stu-id="e8ffb-134">**Note:** The preceding instructions don't work with `ITicketStore` (`CookieAuthenticationOptions.SessionStore`).</span></span>  <span data-ttu-id="e8ffb-135">Pour plus d’informations, consultez [ce problème GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span><span class="sxs-lookup"><span data-stu-id="e8ffb-135">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span></span>

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="e8ffb-136">Partager l’authentification cookie s sans ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="e8ffb-136">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="e8ffb-137">Lorsque vous utilisez cookie s directement sans ASP.NET Core Identity , configurez la protection des données et l’authentification dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e8ffb-137">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e8ffb-138">Dans l’exemple suivant, le type d’authentification est défini sur `Identity.Application` :</span><span class="sxs-lookup"><span data-stu-id="e8ffb-138">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="e8ffb-139">Partager des cookie s entre différents chemins d’accès de base</span><span class="sxs-lookup"><span data-stu-id="e8ffb-139">Share cookies across different base paths</span></span>

<span data-ttu-id="e8ffb-140">Une authentification cookie utilise [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) comme valeur par défaut [ Cookie . Chemin d’accès](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="e8ffb-140">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="e8ffb-141">Si le de l’application cookie doit être partagé entre différents chemins de base, `Path` doit être substitué :</span><span class="sxs-lookup"><span data-stu-id="e8ffb-141">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="e8ffb-142">Partager des cookie s entre des sous-domaines</span><span class="sxs-lookup"><span data-stu-id="e8ffb-142">Share cookies across subdomains</span></span>

<span data-ttu-id="e8ffb-143">Lorsque vous hébergez des applications qui partagent des cookie s entre des sous-domaines, spécifiez un domaine commun dans le [ Cookie . ](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)Propriété de domaine.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-143">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="e8ffb-144">Pour partager des cookie s entre les applications sur `contoso.com` , telles que `first_subdomain.contoso.com` et `second_subdomain.contoso.com` , spécifiez le `Cookie.Domain` en tant que `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="e8ffb-144">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="e8ffb-145">Chiffrer les clés de protection des données au repos</span><span class="sxs-lookup"><span data-stu-id="e8ffb-145">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="e8ffb-146">Pour les déploiements de production, configurez `DataProtectionProvider` pour chiffrer les clés au repos avec DPAPI ou un certificat X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-146">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="e8ffb-147">Pour plus d'informations, consultez <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-147">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="e8ffb-148">Dans l’exemple suivant, une empreinte numérique de certificat est fournie à <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="e8ffb-148">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="e8ffb-149">Partager les authentifications cookie entre ASP.net 4. x et les applications ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="e8ffb-149">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="e8ffb-150">Les applications ASP.NET 4. x qui utilisent Cookie l’intergiciel d’authentification Katana peuvent être configurées pour générer des s d’authentification cookie compatibles avec l' Cookie intergiciel (middleware) d’authentification ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-150">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="e8ffb-151">Cela permet de mettre à niveau les applications individuelles d’un site de grande taille en plusieurs étapes, tout en fournissant une authentification unique fluide sur le site.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-151">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="e8ffb-152">Quand une application utilise Cookie l’intergiciel (middleware) d’authentification Katana, elle appelle `UseCookieAuthentication` dans le fichier *Startup.auth.cs* du projet.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-152">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="e8ffb-153">Les projets d’application Web ASP.NET 4. x créés avec Visual Studio 2013 et versions ultérieures utilisent par Cookie défaut l’intergiciel d’authentification Katana.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-153">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="e8ffb-154">Bien que `UseCookieAuthentication` soit obsolète et non prise en charge pour les applications ASP.net Core, l’appel `UseCookieAuthentication` de dans une application ASP.net 4. x qui utilise Cookie l’intergiciel (middleware) d’authentification Katana est valide.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-154">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="e8ffb-155">Une application ASP.NET 4. x doit cibler .NET Framework 4.5.1 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-155">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="e8ffb-156">Dans le cas contraire, l’installation des packages NuGet nécessaires échoue.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-156">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="e8ffb-157">Pour partager l’authentification cookie entre une application ASP.net 4. x et une application ASP.net Core, configurez l’application ASP.net Core comme indiqué dans la section [share Authentication cookie s from ASP.net Core Apps](#share-authentication-cookies-with-aspnet-core-identity) , puis configurez l’application ASP.net 4. x comme suit.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-157">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="e8ffb-158">Vérifiez que les packages de l’application sont mis à jour avec les versions les plus récentes.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-158">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="e8ffb-159">Installez le package [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) dans chaque application ASP.net 4. x.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-159">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="e8ffb-160">Recherchez et modifiez l’appel à `UseCookieAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="e8ffb-160">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="e8ffb-161">Modifiez le cookie nom pour qu’il corresponde au nom utilisé par le Cookie middleware d’authentification ASP.net Core ( `.AspNet.SharedCookie` dans l’exemple).</span><span class="sxs-lookup"><span data-stu-id="e8ffb-161">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="e8ffb-162">Dans l’exemple suivant, le type d’authentification est défini sur `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="e8ffb-162">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="e8ffb-163">Fournissez une instance d’un `DataProtectionProvider` initialisé à l’emplacement de stockage de la clé de protection des données commune.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-163">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="e8ffb-164">Confirmez que le nom de l’application est défini sur le nom d’application commun utilisé par toutes les applications qui partagent les s d’authentification cookie ( `SharedCookieApp` dans l’exemple).</span><span class="sxs-lookup"><span data-stu-id="e8ffb-164">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="e8ffb-165">Si vous ne définissez pas `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` et `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , définissez <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> sur une revendication qui distingue les utilisateurs uniques.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-165">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="e8ffb-166">*App_Start/Startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="e8ffb-166">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="e8ffb-167">Lors de la génération d’une identité d’utilisateur, le type d’authentification ( `Identity.Application` ) doit correspondre au type défini dans l' `AuthenticationType` ensemble avec `UseCookieAuthentication` dans *App_Start/Startup.auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-167">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="e8ffb-168">*Modèles/ Identity Models.cs*:</span><span class="sxs-lookup"><span data-stu-id="e8ffb-168">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="e8ffb-169">Utiliser une base de données utilisateur commune</span><span class="sxs-lookup"><span data-stu-id="e8ffb-169">Use a common user database</span></span>

<span data-ttu-id="e8ffb-170">Lorsque les applications utilisent le même Identity schéma (même version de Identity ), vérifiez que le Identity système de chaque application pointe vers la même base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-170">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="e8ffb-171">Dans le cas contraire, le système d’identité génère des erreurs au moment de l’exécution lorsqu’il tente de faire correspondre les informations dans l’authentification cookie par rapport aux informations contenues dans sa base de données.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-171">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="e8ffb-172">Lorsque le Identity schéma est différent parmi les applications, généralement parce que les applications utilisent des Identity versions différentes, le partage d’une base de données commune basée sur la dernière version de Identity n’est pas possible sans remappage et ajout de colonnes dans les schémas d’autres applications Identity .</span><span class="sxs-lookup"><span data-stu-id="e8ffb-172">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="e8ffb-173">Il est souvent plus efficace de mettre à niveau les autres applications pour utiliser la dernière Identity version afin qu’une base de données commune puisse être partagée par les applications.</span><span class="sxs-lookup"><span data-stu-id="e8ffb-173">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e8ffb-174">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="e8ffb-174">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
