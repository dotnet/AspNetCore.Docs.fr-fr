---
title: Implémentations de serveurs web dans ASP.NET Core
author: rick-anderson
description: Découvrez les serveurs web Kestrel et HTTP.sys pour ASP.NET Core. Découvrez comment choisir un serveur et quand utiliser un serveur proxy inverse.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: a49388c73f2ec0ea03b35dbba1575ce70a4e1512
ms.sourcegitcommit: 75db2f684a9302b0be7925eab586aa091c6bd19f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99238329"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Implémentations de serveurs web dans ASP.NET Core

De [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) et [Chris Ross](https://github.com/Tratcher)

Une application ASP.NET Core s’exécute avec une implémentation de serveur HTTP in-process. L’implémentation du serveur écoute les requêtes HTTP et les expose à l’application sous forme d’ensemble de [fonctionnalités de requêtes](xref:fundamentals/request-features) composées dans un <xref:Microsoft.AspNetCore.Http.HttpContext>.

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core est fourni avec les composants suivants :

* [Serveur Kestrel](xref:fundamentals/servers/kestrel) : implémentation de serveur HTTP multiplateforme par défaut. Kestrel fournit les meilleures performances et l’utilisation de la mémoire, mais il ne dispose pas de certaines des fonctionnalités avancées de HTTP.sys. Pour plus d’informations, consultez [Kestrel et HTTP.sys](#korh) dans la section suivante.
* Le serveur HTTP IIS est un [serveur in-process](#hosting-models) pour IIS.
* [Serveur HTTP.sys](xref:fundamentals/servers/httpsys) : serveur HTTP exclusivement Windows basé sur le [pilote de noyau HTTP.Sys et l’API Serveur HTTP](/windows/desktop/Http/http-api-start-page).

Lorsque vous utilisez [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), l’application s’exécute :

* Dans le même processus que le processus de travail IIS (le [modèle d’hébergement in-process](#hosting-models)) avec le serveur http IIS. *In-process* est la configuration recommandée.
* Ou dans un processus séparé du processus Worker IIS (le [mode d’hébergement out-of-process](#hosting-models)) avec le [serveur Kestrel](#kestrel).

Le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) est un module IIS natif qui gère les requêtes IIS natives entre IIS et le serveur HTTP IIS in-process ou Kestrel. Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module>.

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a>Kestrel et HTTP.sys

Kestrel présente les avantages suivants par rapport à HTTP.sys :

  * Meilleures performances et utilisation de la mémoire.
  * Multiplateforme
  * L’agilité, elle est développée et corrigée indépendamment du système d’exploitation.
  * Configuration du port et du protocole TLS par programmation
  * Extensibilité qui autorise les protocoles tels que [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) et les transports alternatifs.

Http.Sys fonctionne comme un composant partagé en mode noyau avec les fonctionnalités suivantes que Kestrel n’a pas :

  * Partage de port
  * Authentification Windows en mode noyau. [Kestrel prend en charge uniquement l’authentification en mode utilisateur](xref:security/authentication/windowsauth#kestrel).
  * Proxy rapide via les transferts de file d’attente
  * Transmission de fichier directe
  * Mise en cache des réponses

## <a name="hosting-models"></a>Modèles d'hébergement

En utilisant l’hébergement in-process, une application ASP.NET Core s’exécute dans le même processus que son processus de travail IIS. L’hébergement in-process offre de meilleures performances que l’hébergement out-of-process parce que les requêtes n’ont pas de proxy sur l’adaptateur de bouclage, interface réseau qui retourne du trafic réseau sortant à la même machine. IIS s’occupe de la gestion des processus par l’intermédiaire du [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Avec l’hébergement out-of-process, les applications ASP.NET Core s’exécutent dans un processus distinct du processus de travail IIS, et le module s’occupe de la gestion de processus. Le module démarre le processus pour l’application ASP.NET Core quand la première requête arrive, et il redémarre l’application si elle s’arrête ou se bloque. Il s’agit essentiellement du même comportement que celui des applications s’exécutant in-process, et qui sont gérées par le [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Pour plus d’informations et pour obtenir de l’aide sur la configuration, consultez les rubriques suivantes :

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core est fourni avec le [serveur Kestrel](xref:fundamentals/servers/kestrel) qui est le serveur HTTP multiplateforme par défaut.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core est fourni avec le [serveur Kestrel](xref:fundamentals/servers/kestrel) qui est le serveur HTTP multiplateforme par défaut.

---

::: moniker-end

## <a name="kestrel"></a>Kestrel

 [Serveur Kestrel](xref:fundamentals/servers/kestrel) : implémentation de serveur HTTP multiplateforme par défaut. Kestrel fournit les meilleures performances et l’utilisation de la mémoire, mais il ne dispose pas de certaines des fonctionnalités avancées de HTTP.sys. Pour plus d’informations, consultez [Kestrel et HTTP.sys](#korh) dans ce document.

Utilisez Kestrel :

* Par lui même, c’est-à-dire en tant que serveur de périphérie traitant les requêtes en provenance directe d’un réseau, notamment d’Internet.

  ![Kestrel communique directement avec Internet sans serveur proxy inverse](kestrel/_static/kestrel-to-internet2.png)

* Avec un *serveur proxy inverse*, comme [IIS (Internet Information Services)](https://www.iis.net/), [Nginx](https://nginx.org) ou [Apache](https://httpd.apache.org/). Un serveur proxy inverse reçoit les requêtes HTTP en provenance d’Internet et les transmet à Kestrel.

  ![Kestrel communique indirectement avec Internet via un serveur proxy inverse, par exemple IIS, Nginx ou Apache](kestrel/_static/kestrel-to-internet.png)

L’hébergement &mdash; de la configuration avec ou sans serveur proxy inverse &mdash; est pris en charge.

Pour des conseils de configuration de Kestrel et des informations sur le moment d’utiliser Kestrel dans une configuration de proxy inverse, consultez <xref:fundamentals/servers/kestrel>.

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

ASP.NET Core est fourni avec les composants suivants :

* [Serveur Kestrel](xref:fundamentals/servers/kestrel) : serveur HTTP multiplateforme par défaut.
* [Serveur HTTP.sys](xref:fundamentals/servers/httpsys) : serveur HTTP exclusivement Windows basé sur le [pilote de noyau HTTP.Sys et l’API Serveur HTTP](/windows/desktop/Http/http-api-start-page).

Lorsqu’[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) sont utilisés, l’application s’exécute dans un processus séparé du processus Worker IIS (le mode d’hébergement *out-of-process*) avec le [serveur Kestrel](#kestrel).

Étant donné que les applications ASP.NET Core s’exécutent dans un processus distinct du processus de travail IIS, le module s’occupe de la gestion du processus. Le module démarre le processus pour l’application ASP.NET Core quand la première requête arrive, et il redémarre l’application si elle s’arrête ou se bloque. Il s’agit essentiellement du même comportement que celui des applications s’exécutant in-process, et qui sont gérées par le [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Le schéma suivant illustre la relation entre IIS, le module ASP.NET Core et une application hébergée hors processus :

![Module ASP.NET Core](_static/ancm-outofprocess.png)

Les requêtes arrivent du web au pilote HTTP.sys en mode noyau. Le pilote route les requêtes vers IIS sur le port configuré du site web, généralement 80 (HTTP) ou 443 (HTTPS). Le module transfère les requêtes à Kestrel sur un port aléatoire pour l’application, qui n’est ni le port 80 ni le port 443.

Le module spécifie le port via une variable d’environnement au démarrage, et l' [intergiciel (middleware) d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configure le serveur pour l’écoute `http://localhost:{port}` . Des vérifications supplémentaires sont effectuées, et les requêtes qui ne proviennent pas du module sont rejetées. Le module ne prend pas en charge le transfert HTTPS : les requêtes sont donc transférées via HTTP, même si IIS les reçoit via HTTPS.

Dès que Kestrel sélectionne la requête dans le module, celle-ci est envoyée (push) dans le pipeline de middlewares d’ASP.NET Core. Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application. L’intergiciel (middleware) ajouté par l’intégration d’IIS met à jour le schéma, l’adresse IP distante et la base du chemin pour prendre en compte le transfert de la requête à Kestrel. La réponse de l’application est ensuite repassée à IIS, qui la renvoie au client HTTP à l’origine de la requête.

Pour des conseils de configuration d’IIS et du module ASP.NET Core, consultez les rubriques suivantes :

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core est fourni avec le [serveur Kestrel](xref:fundamentals/servers/kestrel) qui est le serveur HTTP multiplateforme par défaut.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core est fourni avec le [serveur Kestrel](xref:fundamentals/servers/kestrel) qui est le serveur HTTP multiplateforme par défaut.

---

::: moniker-end

### <a name="nginx-with-kestrel"></a>Nginx avec Kestrel

Pour plus d’informations sur l’utilisation de Nginx sur Linux comme serveur proxy inverse pour Kestrel, consultez <xref:host-and-deploy/linux-nginx>.

### <a name="apache-with-kestrel"></a>Apache avec Kestrel

Pour plus d’informations sur l’utilisation d’Apache sur Linux comme serveur proxy inverse pour Kestrel, consultez <xref:host-and-deploy/linux-apache>.

## <a name="httpsys"></a>HTTP.sys

Si vos applications ASP.NET Core sont exécutées sur Windows, HTTP.sys est une alternative à Kestrel. Kestrel est recommandé sur HTTP.sys, sauf si l’application requiert des fonctionnalités qui ne sont pas disponibles dans Kestrel. Pour plus d’informations, consultez <xref:fundamentals/servers/httpsys>.

![HTTP.sys communique directement avec Internet](httpsys/_static/httpsys-to-internet.png)

HTTP.sys peut également être utilisé pour les applications qui sont exposées seulement à un réseau interne.

![HTTP.sys communique directement avec le réseau interne](httpsys/_static/httpsys-to-internal.png)

Pour obtenir des conseils de configuration de HTTP.sys, consultez <xref:fundamentals/servers/httpsys>.

## <a name="aspnet-core-server-infrastructure"></a>Infrastructure serveur d’ASP.NET Core

Le <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> disponible dans la méthode `Startup.Configure` expose la propriété <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> du type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>. Kestrel et HTTP.sys exposent une seule fonctionnalité chacun, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, mais des implémentations serveur différentes peuvent exposer des fonctionnalités supplémentaires.

`IServerAddressesFeature` permet de déterminer quel est le port lié à l’implémentation serveur au moment de l’exécution.

## <a name="custom-servers"></a>Serveurs personnalisés

Si les serveurs intégrés ne répondent pas aux spécifications de l’application, vous pouvez créer une implémentation serveur personnalisée. Le [guide OWIN (Open Web Interface pour .NET)](xref:fundamentals/owin) montre comment écrire une implémentation <xref:Microsoft.AspNetCore.Hosting.Server.IServer> basée sur [Nowin](https://github.com/Bobris/Nowin). Seules les interfaces des fonctionnalités utilisées par l’application nécessitent une implémentation, bien qu’au minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> et <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> doivent être pris en charge.

## <a name="server-startup"></a>Démarrage du serveur

Le serveur est lancé lorsque l’environnement de développement intégré (IDE) ou l’éditeur démarre l’application :

* [Visual Studio](https://visualstudio.microsoft.com): les profils de lancement peuvent être utilisés pour démarrer l’application et le serveur avec [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [module ASP.net Core](xref:host-and-deploy/aspnet-core-module) ou la console.
* [Visual Studio code](https://code.visualstudio.com/): l’application et le serveur sont démarrés par [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), ce qui active le débogueur CoreCLR.
* [Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/): l’application et le serveur sont démarrés par le [débogueur mono Soft-Mode](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).

Lors du lancement de l’application à partir d’une invite de commandes dans le dossier du projet, [dotnet run](/dotnet/core/tools/dotnet-run) lance l’application et le serveur (Kestrel et HTTP.sys uniquement). La configuration est spécifiée par l’option `-c|--configuration`, qui est définie sur `Debug` (par défaut) ou sur `Release`.

Un *launchSettings.jssur* fichier fournit une configuration lors du lancement d’une application avec `dotnet run` ou avec un débogueur intégré à des outils, tels que Visual Studio. Si des profils de lancement sont présents dans un *launchSettings.jssur* le fichier, utilisez l' `--launch-profile {PROFILE NAME}` option avec la `dotnet run` commande ou sélectionnez le profil dans Visual Studio. Pour plus d’informations, consultez les rubriques [dotnet run](/dotnet/core/tools/dotnet-run) et [Package de distribution de .NET Core](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>Assistance HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est pris en charge avec ASP.NET Core dans les scénarios de déploiement suivants :

::: moniker range=">= aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel/http2)
  * Système d’exploitation
    * Windows Server 2016/Windows 10 ou version ultérieure&dagger;
    * Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)
    * HTTP/2 sera pris en charge sur macOS dans une prochaine version.
  * Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 ou version ultérieure
  * Version cible de .Net Framework : non applicable aux déploiements HTTP.sys.
* [IIS (in-process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure
  * Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure
* [IIS (out-of-process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure
  * Les connexions au serveur périphérique public utilisent HTTP/2, mais la connexion de proxy inverse à Kestrel utilise HTTP/1.1.
  * Version cible de .Net Framework : non applicable aux déploiements IIS out-of-process.

&dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1. La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée. Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * Système d’exploitation
    * Windows Server 2016/Windows 10 ou version ultérieure&dagger;
    * Linux avec OpenSSL 1.0.2 ou version ultérieure (par exemple, Ubuntu 16.04 ou version ultérieure)
    * HTTP/2 sera pris en charge sur macOS dans une prochaine version.
  * Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 ou version ultérieure
  * Version cible de .Net Framework : non applicable aux déploiements HTTP.sys.
* [IIS (in-process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure
  * Version cible de .Net Framework : .NET Core 2.2 ou version ultérieure
* [IIS (out-of-process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure
  * Les connexions au serveur périphérique public utilisent HTTP/2, mais la connexion de proxy inverse à Kestrel utilise HTTP/1.1.
  * Version cible de .Net Framework : non applicable aux déploiements IIS out-of-process.

&dagger;Kestrel propose une prise en charge limitée de HTTP/2 sous Windows Server 2012 R2 et Windows 8.1. La prise en charge est limitée car la liste des suites de chiffrement TLS prises en charge sur ces systèmes d’exploitation est limitée. Un certificat généré à l’aide d’Elliptic Curve Digital Signature algorithme (ECDSA) peut être requis pour sécuriser les connexions TLS.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 ou version ultérieure
  * Version cible de .Net Framework : non applicable aux déploiements HTTP.sys.
* [IIS (out-of-process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure
  * Les connexions au serveur périphérique public utilisent HTTP/2, mais la connexion de proxy inverse à Kestrel utilise HTTP/1.1.
  * Version cible de .Net Framework : non applicable aux déploiements IIS out-of-process.

::: moniker-end

Une connexion HTTP/2 doit utiliser la [négociation du protocole de la couche d’application (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) et TLS 1.2 ou version ultérieure. Pour plus d’informations, consultez les rubriques qui se rapportent à vos scénarios de déploiement de serveur.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
