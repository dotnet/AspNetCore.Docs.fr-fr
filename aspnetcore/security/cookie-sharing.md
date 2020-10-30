---
title: 'Partager les authentifications :::no-loc(cookie)::: entre les applications ASP.net'
author: rick-anderson
description: 'Découvrez comment partager des authentifications :::no-loc(cookie)::: entre ASP.net 4. x et des applications ASP.net core.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/:::no-loc(cookie):::-sharing
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059687"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="5fc5f-103">Partager les authentifications :::no-loc(cookie)::: entre les applications ASP.net</span><span class="sxs-lookup"><span data-stu-id="5fc5f-103">Share authentication :::no-loc(cookie):::s among ASP.NET apps</span></span>

<span data-ttu-id="5fc5f-104">Par [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5fc5f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5fc5f-105">Les sites Web sont souvent constitués d’applications Web individuelles qui fonctionnent ensemble.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="5fc5f-106">Pour fournir une expérience d’authentification unique (SSO), les applications Web d’un site doivent partager les :::no-loc(cookie)::: s d’authentification.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication :::no-loc(cookie):::s.</span></span> <span data-ttu-id="5fc5f-107">Pour prendre en charge ce scénario, la pile de protection des données permet de partager :::no-loc(cookie)::: l’authentification Katana et les :::no-loc(cookie)::: tickets d’authentification ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-107">To support this scenario, the data protection stack allows sharing Katana :::no-loc(cookie)::: authentication and ASP.NET Core :::no-loc(cookie)::: authentication tickets.</span></span>

<span data-ttu-id="5fc5f-108">Dans les exemples suivants :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-108">In the examples that follow:</span></span>

* <span data-ttu-id="5fc5f-109">Le nom d’authentification :::no-loc(cookie)::: est défini sur la valeur courante `.AspNet.Shared:::no-loc(Cookie):::` .</span><span class="sxs-lookup"><span data-stu-id="5fc5f-109">The authentication :::no-loc(cookie)::: name is set to a common value of `.AspNet.Shared:::no-loc(Cookie):::`.</span></span>
* <span data-ttu-id="5fc5f-110">`AuthenticationType`Est défini sur `:::no-loc(Identity):::.Application` explicitement ou par défaut.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-110">The `AuthenticationType` is set to `:::no-loc(Identity):::.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="5fc5f-111">Un nom d’application commun est utilisé pour permettre au système de protection des données de partager des clés de protection des données ( `Shared:::no-loc(Cookie):::App` ).</span><span class="sxs-lookup"><span data-stu-id="5fc5f-111">A common app name is used to enable the data protection system to share data protection keys (`Shared:::no-loc(Cookie):::App`).</span></span>
* <span data-ttu-id="5fc5f-112">`:::no-loc(Identity):::.Application` est utilisé comme schéma d’authentification.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-112">`:::no-loc(Identity):::.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="5fc5f-113">Quel que soit le schéma utilisé, il doit être utilisé de manière cohérente *dans et dans* les applications partagées, :::no-loc(cookie)::: soit en tant que modèle par défaut, soit en le définissant explicitement.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-113">Whatever scheme is used, it must be used consistently *within and across* the shared :::no-loc(cookie)::: apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="5fc5f-114">Le schéma est utilisé lors du chiffrement et du déchiffrement de :::no-loc(cookie)::: s, de sorte qu’un schéma cohérent doit être utilisé entre les applications.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-114">The scheme is used when encrypting and decrypting :::no-loc(cookie):::s, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="5fc5f-115">Un emplacement de stockage de [clé de protection des données](xref:security/data-protection/implementation/key-management) commun est utilisé.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="5fc5f-116">Dans ASP.NET Core Apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> est utilisé pour définir l’emplacement de stockage de la clé.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="5fc5f-117">Dans .NET Framework Apps, :::no-loc(Cookie)::: l’intergiciel d’authentification utilise une implémentation de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="5fc5f-117">In .NET Framework apps, :::no-loc(Cookie)::: Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="5fc5f-118">`DataProtectionProvider` fournit des services de protection des données pour le chiffrement et le déchiffrement des :::no-loc(cookie)::: données de charge utile d’authentification.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication :::no-loc(cookie)::: payload data.</span></span> <span data-ttu-id="5fc5f-119">L' `DataProtectionProvider` instance est isolée du système de protection des données utilisé par d’autres parties de l’application.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="5fc5f-120">[DataProtectionProvider. Create (System. IO. DirectoryInfo, action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepte un <xref:System.IO.DirectoryInfo> pour spécifier l’emplacement du stockage de la clé de protection des données.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="5fc5f-121">`DataProtectionProvider` requiert le package NuGet [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="5fc5f-122">Dans ASP.NET Core applications 2. x, référencez le [AspNetCore](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="5fc5f-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="5fc5f-123">Dans .NET Framework applications, ajoutez une référence de package à [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="5fc5f-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="5fc5f-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> définit le nom d’application courant.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="5fc5f-125">Partager des authentifications :::no-loc(cookie)::: avec :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="5fc5f-125">Share authentication :::no-loc(cookie):::s with :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="5fc5f-126">Lorsque vous utilisez :::no-loc(ASP.NET Core Identity)::: :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-126">When using :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="5fc5f-127">Les clés de protection des données et le nom de l’application doivent être partagés entre les applications.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="5fc5f-128">Un emplacement de stockage de clés commun est fourni à la <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> méthode dans les exemples suivants.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="5fc5f-129">Utilisez <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> pour configurer un nom d’application partagée commun ( `Shared:::no-loc(Cookie):::App` dans les exemples suivants).</span><span class="sxs-lookup"><span data-stu-id="5fc5f-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`Shared:::no-loc(Cookie):::App` in the following examples).</span></span> <span data-ttu-id="5fc5f-130">Pour plus d'informations, consultez <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="5fc5f-131">Utilisez la <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> méthode d’extension pour configurer le service de protection des données pour les :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="5fc5f-131">Use the <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> extension method to set up the data protection service for :::no-loc(cookie):::s.</span></span>
* <span data-ttu-id="5fc5f-132">Le type d’authentification par défaut est `:::no-loc(Identity):::.Application` .</span><span class="sxs-lookup"><span data-stu-id="5fc5f-132">The default authentication type is `:::no-loc(Identity):::.Application`.</span></span>

<span data-ttu-id="5fc5f-133">Dans `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="5fc5f-134">Partager l’authentification :::no-loc(cookie)::: s sans :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="5fc5f-134">Share authentication :::no-loc(cookie):::s without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="5fc5f-135">Lorsque vous utilisez :::no-loc(cookie)::: s directement sans :::no-loc(ASP.NET Core Identity)::: , configurez la protection des données et l’authentification dans `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5fc5f-135">When using :::no-loc(cookie):::s directly without :::no-loc(ASP.NET Core Identity):::, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5fc5f-136">Dans l’exemple suivant, le type d’authentification est défini sur `:::no-loc(Identity):::.Application` :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-136">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.AddAuthentication(":::no-loc(Identity):::.Application")
    .Add:::no-loc(Cookie):::(":::no-loc(Identity):::.Application", options =>
    {
        options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="5fc5f-137">Partager des :::no-loc(cookie)::: s entre différents chemins d’accès de base</span><span class="sxs-lookup"><span data-stu-id="5fc5f-137">Share :::no-loc(cookie):::s across different base paths</span></span>

<span data-ttu-id="5fc5f-138">Une authentification :::no-loc(cookie)::: utilise [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) comme valeur par défaut [ :::no-loc(Cookie)::: . Chemin d’accès](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span><span class="sxs-lookup"><span data-stu-id="5fc5f-138">An authentication :::no-loc(cookie)::: uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [:::no-loc(Cookie):::.Path](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span></span> <span data-ttu-id="5fc5f-139">Si le de l’application :::no-loc(cookie)::: doit être partagé entre différents chemins de base, `Path` doit être substitué :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-139">If the app's :::no-loc(cookie)::: must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    options.:::no-loc(Cookie):::.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="5fc5f-140">Partager des :::no-loc(cookie)::: s entre des sous-domaines</span><span class="sxs-lookup"><span data-stu-id="5fc5f-140">Share :::no-loc(cookie):::s across subdomains</span></span>

<span data-ttu-id="5fc5f-141">Lorsque vous hébergez des applications qui partagent des :::no-loc(cookie)::: s entre des sous-domaines, spécifiez un domaine commun dans le [ :::no-loc(Cookie)::: . ](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain)Propriété de domaine.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-141">When hosting apps that share :::no-loc(cookie):::s across subdomains, specify a common domain in the [:::no-loc(Cookie):::.Domain](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain) property.</span></span> <span data-ttu-id="5fc5f-142">Pour partager des :::no-loc(cookie)::: s entre les applications sur `contoso.com` , telles que `first_subdomain.contoso.com` et `second_subdomain.contoso.com` , spécifiez le `:::no-loc(Cookie):::.Domain` en tant que `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-142">To share :::no-loc(cookie):::s across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `:::no-loc(Cookie):::.Domain` as `.contoso.com`:</span></span>

```csharp
options.:::no-loc(Cookie):::.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="5fc5f-143">Chiffrer les clés de protection des données au repos</span><span class="sxs-lookup"><span data-stu-id="5fc5f-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="5fc5f-144">Pour les déploiements de production, configurez `DataProtectionProvider` pour chiffrer les clés au repos avec DPAPI ou un certificat X509Certificate.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="5fc5f-145">Pour plus d'informations, consultez <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="5fc5f-146">Dans l’exemple suivant, une empreinte numérique de certificat est fournie à <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="5fc5f-147">Partager les authentifications :::no-loc(cookie)::: entre ASP.net 4. x et les applications ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5fc5f-147">Share authentication :::no-loc(cookie):::s between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="5fc5f-148">Les applications ASP.NET 4. x qui utilisent :::no-loc(Cookie)::: l’intergiciel d’authentification Katana peuvent être configurées pour générer des s d’authentification :::no-loc(cookie)::: compatibles avec l' :::no-loc(Cookie)::: intergiciel (middleware) d’authentification ASP.net core.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-148">ASP.NET 4.x apps that use Katana :::no-loc(Cookie)::: Authentication Middleware can be configured to generate authentication :::no-loc(cookie):::s that are compatible with the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="5fc5f-149">Cela permet de mettre à niveau les applications individuelles d’un site de grande taille en plusieurs étapes, tout en fournissant une authentification unique fluide sur le site.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="5fc5f-150">Quand une application utilise :::no-loc(Cookie)::: l’intergiciel (middleware) d’authentification Katana, elle appelle `Use:::no-loc(Cookie):::Authentication` dans le fichier *Startup.auth.cs* du projet.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-150">When an app uses Katana :::no-loc(Cookie)::: Authentication Middleware, it calls `Use:::no-loc(Cookie):::Authentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="5fc5f-151">Les projets d’application Web ASP.NET 4. x créés avec Visual Studio 2013 et versions ultérieures utilisent par :::no-loc(Cookie)::: défaut l’intergiciel d’authentification Katana.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana :::no-loc(Cookie)::: Authentication Middleware by default.</span></span> <span data-ttu-id="5fc5f-152">Bien que `Use:::no-loc(Cookie):::Authentication` soit obsolète et non prise en charge pour les applications ASP.net Core, l’appel `Use:::no-loc(Cookie):::Authentication` de dans une application ASP.net 4. x qui utilise :::no-loc(Cookie)::: l’intergiciel (middleware) d’authentification Katana est valide.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-152">Although `Use:::no-loc(Cookie):::Authentication` is obsolete and unsupported for ASP.NET Core apps, calling `Use:::no-loc(Cookie):::Authentication` in an ASP.NET 4.x app that uses Katana :::no-loc(Cookie)::: Authentication Middleware is valid.</span></span>

<span data-ttu-id="5fc5f-153">Une application ASP.NET 4. x doit cibler .NET Framework 4.5.1 ou version ultérieure.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="5fc5f-154">Dans le cas contraire, l’installation des packages NuGet nécessaires échoue.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="5fc5f-155">Pour partager l’authentification :::no-loc(cookie)::: entre une application ASP.net 4. x et une application ASP.net Core, configurez l’application ASP.net Core comme indiqué dans la section [share Authentication :::no-loc(cookie)::: s from ASP.net Core Apps](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) , puis configurez l’application ASP.net 4. x comme suit.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-155">To share authentication :::no-loc(cookie):::s between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication :::no-loc(cookie):::s among ASP.NET Core apps](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="5fc5f-156">Vérifiez que les packages de l’application sont mis à jour avec les versions les plus récentes.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="5fc5f-157">Installez le package [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) dans chaque application ASP.net 4. x.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="5fc5f-158">Recherchez et modifiez l’appel à `Use:::no-loc(Cookie):::Authentication` :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-158">Locate and modify the call to `Use:::no-loc(Cookie):::Authentication`:</span></span>

* <span data-ttu-id="5fc5f-159">Modifiez le :::no-loc(cookie)::: nom pour qu’il corresponde au nom utilisé par le :::no-loc(Cookie)::: middleware d’authentification ASP.net Core ( `.AspNet.Shared:::no-loc(Cookie):::` dans l’exemple).</span><span class="sxs-lookup"><span data-stu-id="5fc5f-159">Change the :::no-loc(cookie)::: name to match the name used by the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware (`.AspNet.Shared:::no-loc(Cookie):::` in the example).</span></span>
* <span data-ttu-id="5fc5f-160">Dans l’exemple suivant, le type d’authentification est défini sur `:::no-loc(Identity):::.Application` .</span><span class="sxs-lookup"><span data-stu-id="5fc5f-160">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`.</span></span>
* <span data-ttu-id="5fc5f-161">Fournissez une instance d’un `DataProtectionProvider` initialisé à l’emplacement de stockage de la clé de protection des données commune.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="5fc5f-162">Confirmez que le nom de l’application est défini sur le nom d’application commun utilisé par toutes les applications qui partagent les s d’authentification :::no-loc(cookie)::: ( `Shared:::no-loc(Cookie):::App` dans l’exemple).</span><span class="sxs-lookup"><span data-stu-id="5fc5f-162">Confirm that the app name is set to the common app name used by all apps that share authentication :::no-loc(cookie):::s (`Shared:::no-loc(Cookie):::App` in the example).</span></span>

<span data-ttu-id="5fc5f-163">Si vous ne définissez pas `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` et `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , définissez <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> sur une revendication qui distingue les utilisateurs uniques.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="5fc5f-164">*App_Start/Startup.auth.cs* :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-164">*App_Start/Startup.Auth.cs* :</span></span>

```csharp
app.Use:::no-loc(Cookie):::Authentication(new :::no-loc(Cookie):::AuthenticationOptions
{
    AuthenticationType = ":::no-loc(Identity):::.Application",
    :::no-loc(Cookie):::Name = ".AspNet.Shared:::no-loc(Cookie):::",
    LoginPath = new PathString("/Account/Login"),
    Provider = new :::no-loc(Cookie):::AuthenticationProvider
    {
        OnValidate:::no-loc(Identity)::: =
            SecurityStampValidator
                .OnValidate:::no-loc(Identity):::<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerate:::no-loc(Identity):::: (manager, user) =>
                        user.GenerateUser:::no-loc(Identity):::Async(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("Shared:::no-loc(Cookie):::App"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s." +
                    ":::no-loc(Cookie):::AuthenticationMiddleware",
                ":::no-loc(Identity):::.Application",
                "v2"))),
    :::no-loc(Cookie):::Manager = new Chunking:::no-loc(Cookie):::Manager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="5fc5f-165">Lors de la génération d’une identité d’utilisateur, le type d’authentification ( `:::no-loc(Identity):::.Application` ) doit correspondre au type défini dans l' `AuthenticationType` ensemble avec `Use:::no-loc(Cookie):::Authentication` dans *App_Start/Startup.auth.cs* .</span><span class="sxs-lookup"><span data-stu-id="5fc5f-165">When generating a user identity, the authentication type (`:::no-loc(Identity):::.Application`) must match the type defined in `AuthenticationType` set with `Use:::no-loc(Cookie):::Authentication` in *App_Start/Startup.Auth.cs* .</span></span>

<span data-ttu-id="5fc5f-166">*Modèles/ :::no-loc(Identity)::: Models.cs* :</span><span class="sxs-lookup"><span data-stu-id="5fc5f-166">*Models/:::no-loc(Identity):::Models.cs* :</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public async Task<Claims:::no-loc(Identity):::> GenerateUser:::no-loc(Identity):::Async(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // :::no-loc(Cookie):::AuthenticationOptions.AuthenticationType
        var user:::no-loc(Identity)::: = 
            await manager.Create:::no-loc(Identity):::Async(this, ":::no-loc(Identity):::.Application");

        // Add custom user claims here

        return user:::no-loc(Identity):::;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="5fc5f-167">Utiliser une base de données utilisateur commune</span><span class="sxs-lookup"><span data-stu-id="5fc5f-167">Use a common user database</span></span>

<span data-ttu-id="5fc5f-168">Lorsque les applications utilisent le même :::no-loc(Identity)::: schéma (même version de :::no-loc(Identity)::: ), vérifiez que le :::no-loc(Identity)::: système de chaque application pointe vers la même base de données utilisateur.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-168">When apps use the same :::no-loc(Identity)::: schema (same version of :::no-loc(Identity):::), confirm that the :::no-loc(Identity)::: system for each app is pointed at the same user database.</span></span> <span data-ttu-id="5fc5f-169">Dans le cas contraire, le système d’identité génère des erreurs au moment de l’exécution lorsqu’il tente de faire correspondre les informations dans l’authentification :::no-loc(cookie)::: par rapport aux informations contenues dans sa base de données.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication :::no-loc(cookie)::: against the information in its database.</span></span>

<span data-ttu-id="5fc5f-170">Lorsque le :::no-loc(Identity)::: schéma est différent parmi les applications, généralement parce que les applications utilisent des :::no-loc(Identity)::: versions différentes, le partage d’une base de données commune basée sur la dernière version de :::no-loc(Identity)::: n’est pas possible sans remappage et ajout de colonnes dans les schémas d’autres applications :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="5fc5f-170">When the :::no-loc(Identity)::: schema is different among apps, usually because apps are using different :::no-loc(Identity)::: versions, sharing a common database based on the latest version of :::no-loc(Identity)::: isn't possible without remapping and adding columns in other app's :::no-loc(Identity)::: schemas.</span></span> <span data-ttu-id="5fc5f-171">Il est souvent plus efficace de mettre à niveau les autres applications pour utiliser la dernière :::no-loc(Identity)::: version afin qu’une base de données commune puisse être partagée par les applications.</span><span class="sxs-lookup"><span data-stu-id="5fc5f-171">It's often more efficient to upgrade the other apps to use the latest :::no-loc(Identity)::: version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5fc5f-172">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="5fc5f-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
