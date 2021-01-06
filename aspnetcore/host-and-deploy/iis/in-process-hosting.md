---
title: Hébergement in-process avec IIS et ASP.NET Core
author: rick-anderson
description: En savoir plus sur l’hébergement in-process avec IIS et le module ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058478"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a>Hébergement in-process avec IIS et ASP.NET Core 

L’hébergement in-process exécute une application ASP.NET Core dans le même processus que son processus de travail IIS. L’hébergement in-process offre de meilleures performances que l’hébergement out-of-process parce que les requêtes n’ont pas de proxy sur l’adaptateur de bouclage, interface réseau qui retourne du trafic réseau sortant à la même machine.

Le schéma suivant montre la relation entre IIS, le module ASP.NET Core et une application hébergée dans un processus :

![Module ASP.NET Core dans le scénario d’hébergement in-process](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a>Activer l’hébergement in-process

Étant donné que ASP.NET Core 3,0, l’hébergement in-process a été activé par défaut pour toutes les applications déployées sur IIS.

Pour configurer explicitement une application pour l’hébergement in-process, affectez à la `<AspNetCoreHostingModel>` propriété la valeur `InProcess` dans le fichier projet ( `.csproj` ) :

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a>Architecture générale

Le déroulement général d’une requête est le suivant :

1. Une requête arrive du web au pilote HTTP.sys en mode noyau.
1. Le pilote achemine la requête native vers IIS sur le port configuré du site web, généralement 80 (HTTP) ou 443 (HTTPS).
1. Le module ASP.NET Core reçoit la demande native et la transmet au serveur HTTP IIS ( `IISHttpServer` ). Le serveur HTTP IIS est une implémentation du serveur in-process pour IIS qui convertit la requête native en requête managée.

Une fois que le serveur HTTP IIS a traité la requête :

1. La demande est envoyée au pipeline de l’intergiciel (middleware) ASP.NET Core.
1. Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application.
1. La réponse de l’application est retransmise à IIS via le serveur HTTP IIS.
1. IIS envoie la réponse au client qui a initié la demande.

`CreateDefaultBuilder` Ajoute une <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance en appelant la <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> méthode pour démarrer [CoreCLR](/dotnet/standard/glossary#coreclr) et héberger l’application à l’intérieur du processus de travail IIS ( `w3wp.exe` ou `iisexpress.exe` ). Les tests de performance indiquent que l’hébergement d’une application.NET Core in-process fournit un débit de requêtes beaucoup plus élevé que l'hébergement de l’application out-of-process et des requêtes proxy vers [Kestrel](xref:fundamentals/servers/kestrel).

Les applications publiées comme un fichier exécutable unique ne peuvent pas être chargées par le modèle d’hébergement in-process.

## <a name="application-configuration"></a>Configuration de l’application

Pour configurer les options IIS, incluez une configuration de service pour <xref:Microsoft.AspNetCore.Builder.IISServerOptions> dans <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. L’exemple suivant désactive AutomaticAuthentication :

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Option | Default | Paramètre |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | Si `true`, IIS Server définit le `HttpContext.User` authentifié par [Authentification Windows](xref:security/authentication/windowsauth). Si `false`, le serveur fournit uniquement une identité pour `HttpContext.User` et répond aux questions explicitement posées par `AuthenticationScheme`. L’authentification Windows doit être activée dans IIS pour que `AutomaticAuthentication` fonctionne. Pour plus d’informations, consultez [authentification Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName` | `null` | Définit le nom d’affichage montré aux utilisateurs sur les pages de connexion. |
| `AllowSynchronousIO` | `false` | Indique si des e/s synchrones sont autorisées pour `HttpContext.Request` et `HttpContext.Response` . |
| `MaxRequestBodySize` | `30000000` | Récupère ou définit la taille maximale du corps de la demande de `HttpRequest`. Il est à noter que IIS a comme limite `maxAllowedContentLength`, qui doit être traité avant `MaxRequestBodySize` défini dans `IISServerOptions`. Les modifications de `MaxRequestBodySize` n’ont pas d’incidence sur `maxAllowedContentLength`. Pour augmenter `maxAllowedContentLength` , ajoutez une entrée dans le `web.config` pour définir `maxAllowedContentLength` sur une valeur supérieure. Pour plus d’informations, voir [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a>Différences entre l’hébergement in-process et out-of-process

Les caractéristiques suivantes s’appliquent lors de l’hébergement in-process :

* Le serveur HTTP IIS (`IISHttpServer`) est utilisé à la place du serveur [Kestrel](xref:fundamentals/servers/kestrel). Pour in-process, les [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) appels <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> à :

  * Enregistrer `IISHttpServer`.
  * Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.
  * Configurer l’hôte pour capturer des erreurs de démarrage.

* L' [ `requestTimeout` attribut](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) ne s’applique pas à l’hébergement in-process.

* Le partage d’un pool d’applications entre les applications n’est pas pris en charge. Utilisez un pool d’applications par application.

* L’architecture (nombre de bits) de l’application et le runtime installé (x64 ou x86) doivent correspondre à l’architecture du pool d’applications. Par exemple, les applications publiées pour 32 bits (x86) doivent avoir 32 bits activés pour leurs pools d’applications IIS. Pour plus d’informations, consultez la section [créer le site IIS](xref:tutorials/publish-to-iis#create-the-iis-site) .

* Les déconnexions du client sont détectées. Le [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) jeton d’annulation est annulé lorsque le client se déconnecte.

* Dans le cas d’un hébergement in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> n’est pas appelé en interne pour initialiser un utilisateur. Par conséquent, une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> utilisée pour transformer les revendications après chaque authentification n’est pas activée par défaut. Si vous transformez les revendications avec une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, appelez <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> pour ajouter des services d’authentification :

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
* [Les déploiements de package Web (à fichier unique)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) ne sont pas pris en charge.
