---
title: Hébergement out-of-process avec IIS et ASP.NET Core
author: rick-anderson
description: En savoir plus sur l’hébergement hors processus avec IIS et le module ASP.NET Core.
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060415"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a>Hébergement out-of-process avec IIS et ASP.NET Core 

Étant donné que ASP.NET Core applications s’exécutent dans un processus distinct du processus de travail IIS, le module ASP.NET Core gère la gestion des processus. Le module démarre le processus pour l’application ASP.NET Core quand la première requête arrive, et il redémarre l’application si elle s’arrête ou se bloque. Il s’agit essentiellement du même comportement que celui des applications s’exécutant in-process, et qui sont gérées par le [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Le schéma suivant illustre la relation entre IIS, le module ASP.NET Core et une application hébergée hors processus :

![Module ASP.NET Core dans le scénario d’hébergement out-of-process](index/_static/ancm-outofprocess.png)

1. Les requêtes arrivent du web au pilote HTTP.sys en mode noyau.
1. Le pilote achemine les requêtes vers IIS sur le port configuré du site Web. Le port configuré est généralement 80 (HTTP) ou 443 (HTTPs).
1. Le module transfère les demandes à Kestrel sur un port aléatoire pour l’application. Le port aléatoire n’est pas 80 ou 443.

<!-- make this a bullet list -->
Le module ASP.NET Core spécifie le port via une variable d’environnement au démarrage. L' <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configure le serveur pour l’écoute `http://localhost:{PORT}` . Des vérifications supplémentaires sont effectuées, et les requêtes qui ne proviennent pas du module sont rejetées. Le module ne prend pas en charge le transfert HTTPs. Les demandes sont transférées via HTTP, même si elles sont reçues par IIS sur HTTPs.

Une fois que Kestrel a extrait la demande du module, la demande est transmise dans le pipeline de l’intergiciel (middleware) ASP.NET Core. Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application. L’intergiciel (middleware) ajouté par l’intégration d’IIS met à jour le schéma, l’adresse IP distante et la base du chemin pour prendre en compte le transfert de la requête à Kestrel. La réponse de l’application est retransmise à IIS, qui la retransmet au client HTTP qui a initié la demande.

Pour obtenir de l’aide sur la configuration du module ASP.NET Core, consultez <xref:host-and-deploy/aspnet-core-module>.

Pour plus d’informations sur l’hébergement, consultez [Héberger dans ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Configuration de l’application

### <a name="enable-the-iisintegration-components"></a>Activer les composants IISIntegration

Lors de la génération d’un hôte dans `CreateHostBuilder` ( `Program.cs` ), appelez <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> pour activer l’intégration d’IIS :

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

Pour plus d'informations sur le `CreateDefaultBuilder`, consultez <xref:fundamentals/host/generic-host#default-builder-settings>.


**Modèle d’hébergement out-of-process**

Pour configurer les options IIS, incluez une configuration de service pour <xref:Microsoft.AspNetCore.Builder.IISOptions> dans <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. L’exemple suivant empêche l’application d’être renseignée `HttpContext.Connection.ClientCertificate` :

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Option                         | Default | Paramètre |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si la valeur est `true`, le [middleware d’intégration IIS](#enable-the-iisintegration-components) définit l’élément `HttpContext.User` authentifié par [Windows Authentication](xref:security/authentication/windowsauth). Si `false`, l’intergiciel (middleware) fournit uniquement une identité pour `HttpContext.User` et répond aux questions explicitement posées par `AuthenticationScheme`. L’authentification Windows doit être activée dans IIS pour que `AutomaticAuthentication` fonctionne. Pour plus d'informations, consultez la rubrique [Authentification Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Définit le nom d’affichage montré aux utilisateurs sur les pages de connexion. |
| `ForwardClientCertificate`     | `true`  | Si la valeur est `true` et que l’en-tête de requête `MS-ASPNETCORE-CLIENTCERT` est présent, `HttpContext.Connection.ClientCertificate` est renseigné. |


### <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

L' [intergiciel (middleware) d’intégration IIS](#enable-the-iisintegration-components) et le module ASP.net Core sont configurés pour transférer les éléments suivants :

* Schéma (HTTP/HTTPs).
* Adresse IP distante à l’origine de la demande.

L' [intergiciel (middleware) d’intégration IIS](#enable-the-iisintegration-components) configure l’intergiciel (middleware) des en-têtes transférés.

Une configuration supplémentaire peut être nécessaire pour les applications hébergées derrière des serveurs proxy et des équilibreurs de charge supplémentaires. Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).


### <a name="out-of-process-hosting-model"></a>Modèle d’hébergement out-of-process

Pour configurer une application pour l’hébergement hors processus, affectez à la propriété la valeur `<AspNetCoreHostingModel>` `OutOfProcess` dans le fichier projet ( `.csproj` ) :

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

L’hébergement in-process est défini avec `InProcess` , qui est la valeur par défaut.

La valeur de ne respecte pas la `<AspNetCoreHostingModel>` casse, donc `inprocess` et `outofprocess` sont des valeurs valides.

Le serveur [Kestrel](xref:fundamentals/servers/kestrel) est utilisé à la place du serveur HTTP IIS (`IISHttpServer`).

Pour out-of-process, les [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) appels <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> à :

* Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.
* Configurer l’hôte pour capturer des erreurs de démarrage.

### <a name="process-name"></a>Nom du processus

`Process.GetCurrentProcess().ProcessName` signale `w3wp`/`iisexpress` (in-process) ou `dotnet` (out-of-process).

De nombreux modules natifs, comme l’authentification Windows, restent actifs. Pour obtenir plus d’informations sur les modules IIS actifs avec le module ASP.NET Core, consultez <xref:host-and-deploy/iis/modules>.

Le module ASP.NET Core peut également  :

* Définir des variables d’environnement pour le processus de travail.
* Journaliser la sortie de stdout dans un stockage de fichiers pour résoudre les problèmes de démarrage.
* Transférer des jetons d’authentification Windows.
