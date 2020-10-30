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
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059687"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>Partager les authentifications cookie entre les applications ASP.net

Par [Rick Anderson](https://twitter.com/RickAndMSFT)

Les sites Web sont souvent constitués d’applications Web individuelles qui fonctionnent ensemble. Pour fournir une expérience d’authentification unique (SSO), les applications Web d’un site doivent partager les cookie s d’authentification. Pour prendre en charge ce scénario, la pile de protection des données permet de partager cookie l’authentification Katana et les cookie tickets d’authentification ASP.net core.

Dans les exemples suivants :

* Le nom d’authentification cookie est défini sur la valeur courante `.AspNet.SharedCookie` .
* `AuthenticationType`Est défini sur `Identity.Application` explicitement ou par défaut.
* Un nom d’application commun est utilisé pour permettre au système de protection des données de partager des clés de protection des données ( `SharedCookieApp` ).
* `Identity.Application` est utilisé comme schéma d’authentification. Quel que soit le schéma utilisé, il doit être utilisé de manière cohérente *dans et dans* les applications partagées, cookie soit en tant que modèle par défaut, soit en le définissant explicitement. Le schéma est utilisé lors du chiffrement et du déchiffrement de cookie s, de sorte qu’un schéma cohérent doit être utilisé entre les applications.
* Un emplacement de stockage de [clé de protection des données](xref:security/data-protection/implementation/key-management) commun est utilisé.
  * Dans ASP.NET Core Apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> est utilisé pour définir l’emplacement de stockage de la clé.
  * Dans .NET Framework Apps, Cookie l’intergiciel d’authentification utilise une implémentation de <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> . `DataProtectionProvider` fournit des services de protection des données pour le chiffrement et le déchiffrement des cookie données de charge utile d’authentification. L' `DataProtectionProvider` instance est isolée du système de protection des données utilisé par d’autres parties de l’application. [DataProtectionProvider. Create (System. IO. DirectoryInfo, action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepte un <xref:System.IO.DirectoryInfo> pour spécifier l’emplacement du stockage de la clé de protection des données.
* `DataProtectionProvider` requiert le package NuGet [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) :
  * Dans ASP.NET Core applications 2. x, référencez le [AspNetCore](xref:fundamentals/metapackage-app).
  * Dans .NET Framework applications, ajoutez une référence de package à [Microsoft. AspNetCore. dataprotection. extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> définit le nom d’application courant.

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>Partager des authentifications cookie avec ASP.NET Core Identity

Lorsque vous utilisez ASP.NET Core Identity :

* Les clés de protection des données et le nom de l’application doivent être partagés entre les applications. Un emplacement de stockage de clés commun est fourni à la <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> méthode dans les exemples suivants. Utilisez <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> pour configurer un nom d’application partagée commun ( `SharedCookieApp` dans les exemples suivants). Pour plus d'informations, consultez <xref:security/data-protection/configuration/overview>.
* Utilisez la <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> méthode d’extension pour configurer le service de protection des données pour les cookie .
* Le type d’authentification par défaut est `Identity.Application` .

Dans `Startup.ConfigureServices` :

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>Partager l’authentification cookie s sans ASP.NET Core Identity

Lorsque vous utilisez cookie s directement sans ASP.NET Core Identity , configurez la protection des données et l’authentification dans `Startup.ConfigureServices` . Dans l’exemple suivant, le type d’authentification est défini sur `Identity.Application` :

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

## <a name="share-no-loccookies-across-different-base-paths"></a>Partager des cookie s entre différents chemins d’accès de base

Une authentification cookie utilise [HttpRequest. PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) comme valeur par défaut [ Cookie . Chemin d’accès](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Si le de l’application cookie doit être partagé entre différents chemins de base, `Path` doit être substitué :

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>Partager des cookie s entre des sous-domaines

Lorsque vous hébergez des applications qui partagent des cookie s entre des sous-domaines, spécifiez un domaine commun dans le [ Cookie . ](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)Propriété de domaine. Pour partager des cookie s entre les applications sur `contoso.com` , telles que `first_subdomain.contoso.com` et `second_subdomain.contoso.com` , spécifiez le `Cookie.Domain` en tant que `.contoso.com` :

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Chiffrer les clés de protection des données au repos

Pour les déploiements de production, configurez `DataProtectionProvider` pour chiffrer les clés au repos avec DPAPI ou un certificat X509Certificate. Pour plus d'informations, consultez <xref:security/data-protection/implementation/key-encryption-at-rest>. Dans l’exemple suivant, une empreinte numérique de certificat est fournie à <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>Partager les authentifications cookie entre ASP.net 4. x et les applications ASP.net Core

Les applications ASP.NET 4. x qui utilisent Cookie l’intergiciel d’authentification Katana peuvent être configurées pour générer des s d’authentification cookie compatibles avec l' Cookie intergiciel (middleware) d’authentification ASP.net core. Cela permet de mettre à niveau les applications individuelles d’un site de grande taille en plusieurs étapes, tout en fournissant une authentification unique fluide sur le site.

Quand une application utilise Cookie l’intergiciel (middleware) d’authentification Katana, elle appelle `UseCookieAuthentication` dans le fichier *Startup.auth.cs* du projet. Les projets d’application Web ASP.NET 4. x créés avec Visual Studio 2013 et versions ultérieures utilisent par Cookie défaut l’intergiciel d’authentification Katana. Bien que `UseCookieAuthentication` soit obsolète et non prise en charge pour les applications ASP.net Core, l’appel `UseCookieAuthentication` de dans une application ASP.net 4. x qui utilise Cookie l’intergiciel (middleware) d’authentification Katana est valide.

Une application ASP.NET 4. x doit cibler .NET Framework 4.5.1 ou version ultérieure. Dans le cas contraire, l’installation des packages NuGet nécessaires échoue.

Pour partager l’authentification cookie entre une application ASP.net 4. x et une application ASP.net Core, configurez l’application ASP.net Core comme indiqué dans la section [share Authentication cookie s from ASP.net Core Apps](#share-authentication-cookies-with-aspnet-core-identity) , puis configurez l’application ASP.net 4. x comme suit.

Vérifiez que les packages de l’application sont mis à jour avec les versions les plus récentes. Installez le package [Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) dans chaque application ASP.net 4. x.

Recherchez et modifiez l’appel à `UseCookieAuthentication` :

* Modifiez le cookie nom pour qu’il corresponde au nom utilisé par le Cookie middleware d’authentification ASP.net Core ( `.AspNet.SharedCookie` dans l’exemple).
* Dans l’exemple suivant, le type d’authentification est défini sur `Identity.Application` .
* Fournissez une instance d’un `DataProtectionProvider` initialisé à l’emplacement de stockage de la clé de protection des données commune.
* Confirmez que le nom de l’application est défini sur le nom d’application commun utilisé par toutes les applications qui partagent les s d’authentification cookie ( `SharedCookieApp` dans l’exemple).

Si vous ne définissez pas `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` et `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , définissez <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> sur une revendication qui distingue les utilisateurs uniques.

*App_Start/Startup.auth.cs* :

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

Lors de la génération d’une identité d’utilisateur, le type d’authentification ( `Identity.Application` ) doit correspondre au type défini dans l' `AuthenticationType` ensemble avec `UseCookieAuthentication` dans *App_Start/Startup.auth.cs* .

*Modèles/ Identity Models.cs* :

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

## <a name="use-a-common-user-database"></a>Utiliser une base de données utilisateur commune

Lorsque les applications utilisent le même Identity schéma (même version de Identity ), vérifiez que le Identity système de chaque application pointe vers la même base de données utilisateur. Dans le cas contraire, le système d’identité génère des erreurs au moment de l’exécution lorsqu’il tente de faire correspondre les informations dans l’authentification cookie par rapport aux informations contenues dans sa base de données.

Lorsque le Identity schéma est différent parmi les applications, généralement parce que les applications utilisent des Identity versions différentes, le partage d’une base de données commune basée sur la dernière version de Identity n’est pas possible sans remappage et ajout de colonnes dans les schémas d’autres applications Identity . Il est souvent plus efficace de mettre à niveau les autres applications pour utiliser la dernière Identity version afin qu’une base de données commune puisse être partagée par les applications.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:host-and-deploy/web-farm>
