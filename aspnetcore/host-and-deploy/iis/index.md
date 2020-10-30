---
title: Héberger ASP.NET Core sur Windows avec IIS
author: rick-anderson
description: Découvrez comment héberger des applications ASP.NET Core sur Windows Server Internet Information Services (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/index
ms.openlocfilehash: e4a94ca9e3607868f3eb25d88338e8156f7f5206
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061520"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Héberger ASP.NET Core sur Windows avec IIS

::: moniker range=">= aspnetcore-5.0"

Internet Information Services (IIS) est un serveur Web flexible, sécurisé et facile à gérer pour héberger des applications Web, y compris des ASP.NET Core.

## <a name="supported-platforms"></a>Plateformes prises en charge

Les systèmes d’exploitation pris en charge sont les suivants :

* Windows 7 ou version ultérieure
* Windows Server 2012 R2 ou version ultérieure

Les applications publiées pour les déploiements 32 bits (x86) ou 64 bits (x64) sont prises en charge. Déployez une application 32 bits avec un kit SDK .NET Core (x86) de 32 bits, sauf si l’application :

* Nécessite l’espace d’adressage de mémoire virtuelle le plus grand disponible pour une application 64 bits.
* Nécessite la taille de pile IIS la plus grande disponible.
* A des dépendances natives 64 bits.

## <a name="install-the-aspnet-core-modulehosting-bundle"></a>Installer le bundle module/hébergement ASP.NET Core

Téléchargez le programme d’installation à l’aide du lien suivant :

[Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

Pour plus d’informations sur l’installation du module ASP.NET Core ou sur l’installation de différentes versions, consultez [installer le bundle d’hébergement .net Core](xref:host-and-deploy/iis/hosting-bundle).

## <a name="get-started"></a>Prendre en main

Pour découvrir comment héberger un site Web sur IIS, consultez notre [Guide de prise](xref:tutorials/publish-to-iis)en main.

Pour découvrir comment héberger un site Web sur Azure App services, consultez notre [Guide de déploiement sur Azure App service](xref:host-and-deploy/azure-apps/index).

## <a name="deployment-resources-for-iis-administrators"></a>Déploiement de ressources pour les administrateurs IIS

* [Documentation IIS](/iis)
* [Bien démarrer avec le Gestionnaire IIS dans IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Déploiement d’applications .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/troubleshoot>
* <xref:index>
* [Site officiel de Microsoft IIS](https://www.iis.net/)
* [Bibliothèque de contenu technique Windows Server](/windows-server/windows-server)
* [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Pour une expérience de tutoriel sur la publication d'une app ASP.NET Core sur un serveur IIS, voir <xref:tutorials/publish-to-iis>.

[Installer le bundle d’hébergement .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Les systèmes d’exploitation pris en charge sont les suivants :

* Windows 7 ou version ultérieure
* Windows Server 2012 R2 ou version ultérieure

Le [serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement WebListener) ne fonctionne pas dans une configuration de proxy inverse avec IIS. Utilisez le [serveur Kestrel](xref:fundamentals/servers/kestrel).

Pour plus d’informations sur l’hébergement dans Azure, consultez <xref:host-and-deploy/azure-apps/index>.

Pour obtenir des conseils de dépannage, consultez <xref:test/troubleshoot>.

## <a name="supported-platforms"></a>Plateformes prises en charge

Les applications publiées pour les déploiements 32 bits (x86) ou 64 bits (x64) sont prises en charge. Déployez une application 32 bits avec un kit SDK .NET Core (x86) de 32 bits, sauf si l’application :

* Nécessite l’espace d’adressage de mémoire virtuelle le plus grand disponible pour une application 64 bits.
* Nécessite la taille de pile IIS la plus grande disponible.
* A des dépendances natives 64 bits.

Les applications publiées pour 32 bits (x86) doivent avoir 32 bits activés pour leurs pools d’applications IIS. Pour plus d’informations, consultez la section [créer le site IIS](#create-the-iis-site) .

Utilisez un kit SDK .NET Core 64 bits (x64) pour publier une application 64 bits. Un runtime 64 bits doit être présent sur le système hôte.

## <a name="hosting-models"></a>Modèles d'hébergement

### <a name="in-process-hosting-model"></a>Modèle d’hébergement in-process

En utilisant l’hébergement in-process, une application ASP.NET Core s’exécute dans le même processus que son processus de travail IIS. L’hébergement in-process offre de meilleures performances que l’hébergement out-of-process parce que les requêtes n’ont pas de proxy sur l’adaptateur de bouclage, interface réseau qui retourne du trafic réseau sortant à la même machine. IIS s’occupe de la gestion des processus par l’intermédiaire du [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

[Module ASP.net Core](xref:host-and-deploy/aspnet-core-module):

* Effectue l’initialisation de l’application.
  * Charge le [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Appelle `Program.Main`.
* Gère la durée de vie de la requête native IIS.

Le schéma suivant montre la relation entre IIS, le module ASP.NET Core et une application hébergée dans un processus :

![Module ASP.NET Core dans le scénario d’hébergement in-process](index/_static/ancm-inprocess.png)

1. Une requête arrive du web au pilote HTTP.sys en mode noyau.
1. Le pilote achemine la requête native vers IIS sur le port configuré du site web, généralement 80 (HTTP) ou 443 (HTTPS).
1. Le module ASP.NET Core reçoit la demande native et la transmet au serveur HTTP IIS ( `IISHttpServer` ). Le serveur HTTP IIS est une implémentation du serveur in-process pour IIS qui convertit la requête native en requête managée.

Une fois que le serveur HTTP IIS a traité la requête :

1. La demande est envoyée au pipeline de l’intergiciel (middleware) ASP.NET Core.
1. Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application.
1. La réponse de l’application est retransmise à IIS via le serveur HTTP IIS.
1. IIS envoie la réponse au client qui a initié la demande.

L’hébergement in-process est un abonnement pour les applications existantes. Les modèles Web ASP.NET Core utilisent le modèle d’hébergement in-process.

`CreateDefaultBuilder` Ajoute une <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance en appelant la <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> méthode pour démarrer [CoreCLR](/dotnet/standard/glossary#coreclr) et héberger l’application à l’intérieur du processus de travail IIS ( `w3wp.exe` ou `iisexpress.exe` ). Les tests de performance indiquent que l’hébergement d’une application.NET Core in-process fournit un débit de requêtes beaucoup plus élevé que l'hébergement de l’application out-of-process et des requêtes proxy vers [Kestrel](xref:fundamentals/servers/kestrel).

Les applications publiées comme un fichier exécutable unique ne peuvent pas être chargées par le modèle d’hébergement in-process.

### <a name="out-of-process-hosting-model"></a>Modèle d’hébergement out-of-process

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

### <a name="iis-options"></a>Options IIS

**Modèle d’hébergement in-process**

Pour configurer les options IIS Server, incluez une configuration de service pour <xref:Microsoft.AspNetCore.Builder.IISServerOptions> dans <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. L’exemple suivant désactive AutomaticAuthentication :

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Option                         | Default | Paramètre |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si `true`, IIS Server définit le `HttpContext.User` authentifié par [Authentification Windows](xref:security/authentication/windowsauth). Si `false`, le serveur fournit uniquement une identité pour `HttpContext.User` et répond aux questions explicitement posées par `AuthenticationScheme`. L’authentification Windows doit être activée dans IIS pour que `AutomaticAuthentication` fonctionne. Pour plus d’informations, consultez [authentification Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Définit le nom d’affichage montré aux utilisateurs sur les pages de connexion. |
| `AllowSynchronousIO`           | `false` | Indique si des e/s synchrones sont autorisées pour `HttpContext.Request` et `HttpContext.Response` . |
| `MaxRequestBodySize`           | `30000000`  | Récupère ou définit la taille maximale du corps de la demande de `HttpRequest`. Il est à noter que IIS a comme limite `maxAllowedContentLength`, qui doit être traité avant `MaxRequestBodySize` défini dans `IISServerOptions`. Les modifications de `MaxRequestBodySize` n’ont pas d’incidence sur `maxAllowedContentLength`. Pour augmenter `maxAllowedContentLength` , ajoutez une entrée dans le `web.config` pour définir `maxAllowedContentLength` sur une valeur supérieure. Pour plus d’informations, voir [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration). |

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

### <a name="webconfig-file"></a>Fichier `web.config`

Le `web.config` fichier configure le [module ASP.net Core](xref:host-and-deploy/aspnet-core-module). La création, la transformation et la publication du `web.config` fichier sont gérées par une cible MSBuild ( `_TransformWebConfig` ) lors de la publication du projet. Cette cible est présente dans les cibles du SDK web (`Microsoft.NET.Sdk.Web`). Le SDK est défini en haut du fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si un `web.config` fichier n’est pas présent dans le projet, le fichier est créé avec les et corrects `processPath` `arguments` pour configurer le module ASP.net Core, puis déplacé vers la [sortie publiée](xref:host-and-deploy/directory-structure).

Si un `web.config` fichier est présent dans le projet, le fichier est transformé avec les et corrects `processPath` `arguments` pour configurer le module ASP.net Core, puis déplacé vers la sortie publiée. La transformation ne modifie pas les paramètres de configuration IIS dans le fichier.

Le `web.config` fichier peut fournir des paramètres de configuration IIS supplémentaires qui contrôlent les modules IIS actifs. Pour plus d’informations sur les modules IIS capables de traiter les requêtes avec des applications ASP.NET Core, consultez la rubrique [Modules IIS](xref:host-and-deploy/iis/modules).

Pour empêcher le kit de développement logiciel (SDK) Web de transformer le `web.config` fichier, utilisez la `<IsTransformWebConfigDisabled>` propriété dans le fichier projet :

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Lorsque vous désactivez le kit de développement logiciel (SDK) Web pour transformer le fichier, `processPath` et `arguments` doit être défini manuellement par le développeur. Pour plus d'informations, consultez <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>`web.config` emplacement du fichier

Pour configurer correctement le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) , le `web.config` fichier doit être présent au chemin d’accès [racine du contenu](xref:fundamentals/index#content-root) (en général, le chemin d’accès de base de l’application) de l’application déployée. Il s’agit du même emplacement que le chemin physique du site Web fourni à IIS. Le `web.config` fichier est requis à la racine de l’application pour permettre la publication de plusieurs applications à l’aide de Web Deploy.

Des fichiers sensibles existent sur le chemin d’accès physique de l’application, par exemple `{ASSEMBLY}.runtimeconfig.json` , `{ASSEMBLY}.xml` (commentaires de documentation XML) et `{ASSEMBLY}.deps.json` , où l’espace réservé `{ASSEMBLY}` est le nom de l’assembly. Lorsque le `web.config` fichier est présent et que le site démarre normalement, IIS ne traite pas ces fichiers sensibles s’ils sont demandés. Si le `web.config` fichier est manquant, nommé de façon incorrecte ou ne peut pas configurer le site pour un démarrage normal, IIS peut traiter les fichiers sensibles publiquement.

**Le `web.config` fichier doit être présent dans le déploiement à tout moment, correctement nommé et capable de configurer le site pour un démarrage normal. Ne supprimez jamais le `web.config` fichier d’un déploiement de production.**

### <a name="transform-webconfig"></a>Transformer web.config

Si vous devez effectuer une transformation `web.config` lors de la publication, consultez <xref:host-and-deploy/iis/transform-webconfig> . Vous devrez peut-être effectuer une transformation `web.config` sur la publication pour définir des variables d’environnement en fonction de la configuration, du profil ou de l’environnement.

## <a name="iis-configuration"></a>Configuration IIS

**Systèmes d’exploitation Windows Server**

Activez le rôle serveur **Serveur Web (IIS)** et établissez des services de rôle.

1. Utilisez l’Assistant **Ajouter des rôles et des fonctionnalités** par le biais du menu **Gérer** ou du lien dans **Gestionnaire de serveur** . À l’étape **Rôles de serveurs** , cochez la case **Serveur Web (IIS)** .

   ![Le rôle Serveur Web IIS est sélectionné à l’étape Sélectionner des rôles de serveurs.](index/_static/server-roles-ws2016.png)

1. Après l’étape **Fonctionnalités** , l’étape **Services de rôle** se charge pour le serveur Web (IIS). Sélectionnez les services de rôle IIS souhaités ou acceptez les services de rôle par défaut fournis.

   ![Les services de rôle par défaut sont sélectionnés à l’étape Sélectionner des services de rôle.](index/_static/role-services-ws2016.png)

   **Authentification Windows (facultatif)**  
   Pour activer l’authentification Windows, développez les nœuds suivants : sécurité du **serveur Web**  >  **Security** . Sélectionnez la fonctionnalité **Authentification Windows** . Pour plus d’informations, consultez [authentification `<windowsAuthentication>` Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [configurer l’authentification Windows](xref:security/authentication/windowsauth).

   **WebSockets (facultatif)**  
   WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure. Pour activer les WebSockets, développez les nœuds suivants : développement d’applications de **serveur Web**  >  **Application Development** . Sélectionnez la fonctionnalité **Protocole WebSocket** . Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).

1. Validez l’étape de **Confirmation** pour installer les services et le rôle de serveur web. Un redémarrage du serveur/d’IIS n’est pas nécessaire après l’installation du rôle **Serveur Web (IIS)** .

**Systèmes d’exploitation Windows Desktop**

Activez la **Console de gestion IIS** et les **Services World Wide Web** .

1. Naviguez jusqu’à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).

1. Ouvrez le nœud **Internet Information Services** . Ouvrez le nœud **Outils de gestion Web** .

1. Cochez la case **Console de gestion IIS** .

1. Cochez la case **Services World Wide Web** .

1. Acceptez les fonctionnalités par défaut pour **Services World Wide Web** ou personnalisez les fonctionnalités d’IIS.

   **Authentification Windows (facultatif)**  
   Pour activer l’authentification Windows, développez les nœuds suivants : **World Wide webing services**  >  **Security** . Sélectionnez la fonctionnalité **Authentification Windows** . Pour plus d’informations, consultez [authentification \<windowsAuthentication> Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [configurer l’authentification Windows](xref:security/authentication/windowsauth).

   **WebSockets (facultatif)**  
   WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure. Pour activer WebSockets, développez les nœuds suivants : fonctionnalités de développement d’applications **World Wide Web services**  >  **Application Development Features** . Sélectionnez la fonctionnalité **Protocole WebSocket** . Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).

1. Si l’installation d’IIS requiert un redémarrage, redémarrez le système.

![Console de gestion IIS et Services World Wide Web sont sélectionnés dans Fonctionnalités de Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Installer le bundle d’hébergement .NET Core

Installez le *bundle d’hébergement .NET Core* sur le système hôte. L’offre groupée installe le Runtime .NET Core, la bibliothèque .NET Core et le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module). Le module permet aux applications ASP.NET Core de s’exécuter derrière IIS.

> [!IMPORTANT]
> Si le bundle d’hébergement est installé avant IIS, l’installation du bundle doit être réparée. Après avoir installé IIS, réexécutez le programme d’installation du bundle d’hébergement.
>
> Si le bundle d’hébergement est installé après l’installation de la version 64 bits (x 64) de .NET Core, les SDK peuvent apparaître manquants ([Aucun SDK .NET Core n’a été détecté](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Pour résoudre le problème, consultez <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="direct-download-current-version"></a>Téléchargement direct (version actuelle)

Téléchargez le programme d’installation à l’aide du lien suivant :

[Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Versions antérieures du programme d’installation

Pour obtenir une version antérieure du programme d’installation :

1. Accédez à la page [Télécharger .net Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Sélectionnez la version .NET Core de votre choix.
1. Dans la colonne **Run apps - Runtime** , recherchez la ligne de la version du runtime .NET Core souhaitée.
1. Téléchargez le programme d’installation à l’aide du lien d' **hébergement** .

> [!WARNING]
> Certains programmes d’installation contiennent des versions qui sont arrivées à leur fin de vie (EOL) et qui ne sont plus prises en charge par Microsoft. Pour plus d’informations, consultez la [politique de support](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Installer le bundle d’hébergement

1. Exécutez le programme d’installation sur le serveur. Les paramètres suivants sont disponibles lorsque vous exécutez le programme d’installation à partir d’un shell de commande administrateur :

   * `OPT_NO_ANCM=1`: Ignorez l’installation du module ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Ignorez l’installation du Runtime .NET Core. Utilisé lorsque le serveur héberge uniquement [des déploiements autonomes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Ignorez l’installation du Framework partagé ASP.NET (runtime ASP.NET). Utilisé lorsque le serveur héberge uniquement [des déploiements autonomes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Ignorez l’installation des runtimes x86. Utilisez ce paramètre lorsque vous savez que vous n’hébergerez pas d’applications 32 bits. Si vous n’excluez pas d’avoir à héberger des applications 32 bits et 64 bits dans le futur, n'utilisez pas ce paramètre et installez les deux runtimes.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Désactive la vérification de l’utilisation d’une configuration partagée IIS lorsque la configuration partagée ( `applicationHost.config` ) se trouve sur le même ordinateur que l’installation d’IIS. *Disponible uniquement pour les programmes d’installation du pack d’hébergement ASP.NET Core 2.2 ou version ultérieure.* Pour plus d'informations, consultez <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Redémarrez le système ou exécutez les commandes suivantes dans une interface de commande :

   ```console
   net stop was /y
   net start w3svc
   ```
   Le redémarrage d’IIS récupère une modification apportée au CHEMIN D’ACCÈS du système, qui est une variable d’environnement, par le programme d’installation.

ASP.NET Core n’adopte pas le comportement de restauration par progression pour les mises à jour correctives des packages d’infrastructure partagés. Après la mise à niveau de l’infrastructure partagée en installant un nouveau bundle d’hébergement, redémarrez le système ou exécutez les commandes suivantes dans un interpréteur de commandes :

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> Pour plus d’informations sur la configuration partagée IIS, consultez [Module ASP.NET Core avec configuration partagée des services Internet (IIS)](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Installer Web Deploy lors de la publication avec Visual Studio

Quand vous déployez des applications sur un serveur avec [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), installez la dernière version de Web Deploy sur le serveur. Pour installer Web Deploy, utilisez [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) ou obtenez un programme d’installation directement à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). La méthode recommandée est d’utiliser WebPI. WebPI offre une installation autonome et une configuration pour les fournisseurs d’hébergement.

## <a name="create-the-iis-site"></a>Créer le site IIS

1. Sur le système d’hébergement, créez un dossier pour contenir les fichiers et dossiers publiés de l’application. À l’étape suivante, le chemin du dossier est fourni à IIS en tant que chemin d’accès physique à l’application. Pour plus d’informations sur le dossier de déploiement et la disposition d’un fichier d’une application, consultez <xref:host-and-deploy/directory-structure>.

1. Dans le gestionnaire des services Internet, ouvrez le nœud du serveur dans le panneau **connexions** . Cliquez avec le bouton de droite sur le dossier **Sites** . Sélectionnez **Ajouter un site Web** dans le menu contextuel.

1. Spécifiez le **Nom du site** et définissez le **Chemin physique** sur le dossier de déploiement de l’application. Fournissez la configuration de **liaison** et créez le site Web en sélectionnant **OK** :

   ![Spécifiez le nom du site, le chemin physique et le nom d’hôte à l’étape Ajouter un site Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Les liaisons génériques de niveau supérieur (`http://*:80/` et `http://+:80`) ne doivent **pas** être utilisées. Les liaisons génériques de niveau supérieur peuvent exposer votre application à des failles de sécurité. Cela s’applique aux caractères génériques forts et faibles. Utilisez des noms d’hôte explicites plutôt que des caractères génériques. Une liaison générique de sous-domaine (par exemple, `*.mysub.com`) ne présente pas ce risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable). Consultez la [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pour plus d’informations.

1. Sous le nœud du serveur, sélectionnez **Pools d’applications** .

1. Cliquez avec le bouton de droite sur le pool d’applications du site et sélectionnez **Paramètres de base** dans le menu contextuel.

1. Dans la fenêtre **Modifier le pool d’applications** , définissez la **version .NET CLR** sur **Aucun code managé**  :

   ![Définissez Aucun code managé pour la version CLR .NET.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core s’exécute dans un processus séparé et gère l’exécution. ASP.NET Core ne s’appuie pas sur le chargement du CLR de bureau (.NET CLR). Le Common Language Runtime (CoreCLR) principal pour .NET Core est amorcé pour héberger l’application dans le processus de travail. La configuration de la **version CLR .NET** sur **Aucun code managé** est facultative, mais recommandée.

1. *ASP.NET Core 2.2 ou version ultérieure* :

   * Pour un [Déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) 32 bits (x86) publié avec un kit de développement logiciel (SDK) 32 bits qui utilise le [modèle d’hébergement in-process](#in-process-hosting-model), activez le Pool d’applications pour 32 bits. Dans le gestionnaire des services Internet, accédez à **pools d’applications** dans la barre latérale **connexions** . Sélectionnez le pool d’applications de l’application. Dans l’encadré **actions** , sélectionnez **Paramètres avancés** . Définissez **activer les Applications 32 bits** sur `True` . 

   * Pour un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) 64 bits (x64) qui utilise le [modèle d’hébergement In-process](#in-process-hosting-model), désactivez le pool d’applications pour les processus 32 bits (x86). Dans le gestionnaire des services Internet, accédez à **pools d’applications** dans la barre latérale **connexions** . Sélectionnez le pool d’applications de l’application. Dans l’encadré **actions** , sélectionnez **Paramètres avancés** . Définissez **activer les Applications 32 bits** sur `False` . 

1. Vérifiez que l’identité de modèle de processus dispose des autorisations appropriées.

   Si l’identité par défaut du pool d’applications ( **modèle de processus**  >  **Identity** ) est **Identity** remplacée par une autre identité, vérifiez que la nouvelle identité dispose des autorisations nécessaires pour accéder au dossier de l’application, à la base de données et à d’autres ressources requises. Par exemple, le pool d’applications nécessite l’accès en lecture et en écriture aux dossiers dans lesquels l’application lit et écrit des fichiers.

**Configuration de l’authentification Windows (facultatif)**  
Pour plus d'informations, consultez la rubrique [Configurer l’authentification Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Déployer l’application

Déployez l’application dans le dossier **Chemin d’accès physique** IIS qui a été créé dans la section [Créer le site IIS](#create-the-iis-site). [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) est le mécanisme recommandé pour le déploiement, mais plusieurs options existent pour déplacer l’application du dossier du projet `publish` vers le dossier de déploiement du système hôte.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy avec Visual Studio

Pour découvrir comment créer un profil de publication pour une utilisation avec Web Deploy, consultez la rubrique [Profils de publication Visual Studio pour le déploiement d’applications ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles). Si le fournisseur d’hébergement fournit un profil de publication ou prend en charge sa création, téléchargez son profil et importez-le à l’aide de la boîte de dialogue **Publier** de Visual Studio :

![Boîte de dialogue Publier](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy en dehors de Visual Studio

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) peut également être utilisé en dehors de Visual Studio à partir de la ligne de commande. Pour plus d’informations, consultez [Web Deployment Tool (Outil de déploiement Web)](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatives à Web Deploy

Utilisez la méthode de votre choix, telle que la copie manuelle, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) ou [PowerShell](/powershell/), pour déplacer l’application vers le système d’hébergement.

Pour plus d’informations sur le déploiement d’ASP.NET Core sur IIS, consultez la section [Déploiement de ressources pour les administrateurs IIS](#deployment-resources-for-iis-administrators).

## <a name="browse-the-website"></a>Parcourir le site Web

Une fois que l’application est déployée sur le système hôte, envoyez une requête à l’un des points de terminaison publics de l’application.

Dans l’exemple suivant, le site est lié à un **nom d’hôte** IIS de `www.mysite.com` sur le **Port** `80`. Une demande est faite à `http://www.mysite.com` :

![Le navigateur Microsoft Edge a chargé la page de démarrage IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Fichiers de déploiement verrouillés

Les fichiers dans le dossier de déploiement sont verrouillés quand l’application est en cours d’exécution. Les fichiers verrouillés ne peuvent pas être remplacés au cours du déploiement. Pour libérer des fichiers verrouillés dans un déploiement, arrêtez le pool d’applications à l’aide **d’une** des approches suivantes :

* Utilisez Web Deploy et référencez `Microsoft.NET.Sdk.Web` dans le fichier projet. Un `app_offline.htm` fichier est placé à la racine du répertoire de l’application Web. Quand le fichier est présent, le module ASP.NET Core arrête correctement l’application et sert le `app_offline.htm` fichier pendant le déploiement. Pour plus d’informations, consultez les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Arrêtez manuellement le pool d’applications dans le Gestionnaire IIS sur le serveur.
* Utiliser PowerShell pour supprimer `app_offline.htm` (nécessite PowerShell 5 ou version ultérieure) :

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm
  ```

## <a name="data-protection"></a>Protection de données

La [pile de protection des données ASP.NET Core](xref:security/data-protection/introduction) est utilisée par plusieurs [intergiciels (middlewares)](xref:fundamentals/middleware/index) ASP.NET Core, y compris l’intergiciel utilisé dans l’authentification. Même si les API de protection des données ne sont pas appelées par le code de l’utilisateur, la protection des données doit être configurée avec un script de déploiement ou dans un code utilisateur pour créer un [magasin de clés](xref:security/data-protection/implementation/key-management) de chiffrement persistantes. Si la protection des données n’est pas configurée, les clés sont conservées en mémoire et ignorées au redémarrage de l’application.

Si le Key Ring est stocké en mémoire, au redémarrage de l’application :

* Tous les cookie jetons d’authentification de base sont invalidés. 
* Les utilisateurs doivent se reconnecter pour envoyer leur prochaine demande. 
* toutes les données protégées par le Key Ring ne peuvent plus être déchiffrées. Cela peut inclure des [jetons CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) et des ASP.NET Core les de la [MVC cookie ](xref:fundamentals/app-state#tempdata).

Pour configurer la protection des données sous IIS afin de rendre persistante le Key Ring, adoptez **l’une** des approches suivantes :

* **Créer des clés de Registre de la protection des données**

  Les clés de la protection des données utilisées par les applications ASP.NET Core sont stockées dans le registre externe aux applications. Afin de conserver les clés pour une application donnée, créez des clés de Registre pour le pool d’applications.

  Pour les installations IIS autonomes hors d’une batterie de serveurs, le [script PowerShell Provision-AutoGenKeys.ps1 de protection des données](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) peut être utilisé pour chaque pool d’applications utilisé avec une application ASP.NET Core. Ce script crée une clé de Registre dans le Registre HKLM, accessible uniquement pour le compte du processus Worker du pool d’applications de l’application. Les clés sont chiffrées au repos à l’aide de DPAPI avec une clé à l’échelle de la machine.

  Dans les scénarios de batterie de serveurs Web, vous pouvez configurer une application afin qu’elle utilise un chemin UNC pour stocker son Key Ring de protection des données. Par défaut, les clés de protection des données ne sont pas chiffrées. Vérifiez que les autorisations de fichiers pour le partage réseau sont limitées au compte Windows sous lequel s’exécute l’application. Un certificat X509 peut être utilisé pour protéger les clés au repos. Mettez en œuvre un mécanisme permettant aux utilisateurs de charger des certificats : placez les certificats dans le magasin de certificats approuvés de l’utilisateur et vérifiez qu’ils sont disponibles sur toutes les machines où s’exécute l’application de l’utilisateur. Pour plus d’informations, consultez [Configurer la protection des données ASP.NET Core](xref:security/data-protection/configuration/overview).

* **Configurer le pool d’applications IIS pour charger le profil utilisateur**

  Ce paramètre se trouve dans la section **Modèle de processus** sous les **Paramètres avancés** du pool d’applications. Affectez la valeur `True` à **Charger le profil utilisateur** . Lorsqu’elle est définie sur `True`, les clés sont stockées dans le répertoire du profil utilisateur, protégées à l’aide de DPAPI avec une clé propre au compte d’utilisateur utilisé pour le pool d’applications. Les clés sont persistantes dans le dossier *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* .

  L’[attribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) du pool d’applications doit également être activé. La valeur par défaut de `setProfileEnvironment` est `true`. Dans certains scénarios (par exemple pour le système d’exploitation Windows), `setProfileEnvironment` est défini sur `false`. Si les clés ne sont pas stockées dans le répertoire de profil utilisateur comme prévu :

  1. Accédez au dossier *%windir%/system32/inetsrv/config* .
  1. Ouvrez le fichier *applicationHost.config* .
  1. Recherchez l’élément `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Confirmez que l’attribut `setProfileEnvironment` n’est pas présent, ce qui implique que la valeur par défaut est `true`, ou définissez de manière explicite la valeur de l’attribut sur `true`.

* **Utiliser le système de fichiers comme magasin de Key Ring**

  Ajustez le code d’application pour [utiliser le système de fichiers comme magasin de porte-clés](xref:security/data-protection/configuration/overview). Utilisez un certificat X509 pour protéger le Key Ring et vérifiez qu’il s’agit d’un certificat approuvé. S’il s’agit d’un certificat auto-signé, placez-le dans le magasin Racine approuvée.

  Quand vous utilisez IIS dans une batterie de serveurs web :

  * Utilisez un partage de fichiers accessible par tous les ordinateurs.
  * Déployez un certificat X509 sur chaque ordinateur. Configurez la [protection des données dans le code](xref:security/data-protection/configuration/overview).

* **Définir une stratégie au niveau de l’ordinateur pour la protection des données**

  Le système de protection des données offre une prise en charge limitée de la définition d’une [stratégie au niveau de l’ordinateur](xref:security/data-protection/configuration/machine-wide-policy) par défaut pour toutes les applications qui utilisent les API de protection des données. Pour plus d'informations, consultez <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Répertoires virtuels

Les [répertoires virtuels IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ne sont pas pris en charge avec les applications ASP.NET Core. Une application peut être hébergée en tant que [sous-application](#sub-applications).

## <a name="sub-applications"></a>Sous-applications

Une application ASP.NET Core peut être hébergée en tant que [sous-application IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Le chemin d'accès de la sous-application devient partie intégrante de l’URL de l’application racine.

Les liens de ressources statiques au sein de la sous-application doivent utiliser la notation tilde-barre oblique (`~/`). La notation tilde-barre oblique déclenche un [Tag Helper](xref:mvc/views/tag-helpers/intro) afin d’ajouter l’élément pathbase de la sous-application au lien relatif rendu. Pour une sous-application dans `/subapp_path`, une image liée à `src="~/image.png"` est restituée sous la forme `src="/subapp_path/image.png"`. Le composant Static File Middleware de l’application racine ne traite la demande de fichiers statiques. La demande est traitée par le composant Static File Middleware de la sous-application.

Si l’attribut `src` d’une ressource statique est défini sur un chemin d’accès absolu (par exemple, `src="/image.png"`), le lien apparaît sans l’élément pathbase de la sous-application. Le composant Static File Middleware de l’application racine tente de traiter la ressource à partir de l’élément [webroot](xref:fundamentals/index#web-root) de l’application racine, ce qui entraîne une erreur *404 - Introuvable* , sauf si la ressource statique est disponible depuis l’application racine.

Pour héberger une application ASP.NET Core en tant que sous-application d’une autre application ASP.NET Core :

1. Établissez un pool d’applications pour la sous-application. Définissez la **version CLR .NET** sur **Aucun code managé** car la prise en charge du Common Language Runtime Core (CoreCLR) pour Core .Net est démarrée pour héberger l’application dans le processus Worker et non dans le Desktop CLR (CLR .Net).

1. Ajoutez le site racine dans IIS Manager en plaçant la sous-application dans un dossier du site racine.

1. Cliquez avec le bouton droit sur le dossier de la sous-application dans IIS Manager, puis sélectionnez **Convertir en application** .

1. Dans la boîte de dialogue **Ajouter une application** , utilisez le bouton **Sélectionner** de l’option **Pool d’applications** pour affecter le pool d’applications que vous avez créé pour la sous-application. Sélectionnez **OK** .

L’attribution d’un pool d’applications distinct à la sous-application est obligatoire lorsque vous utilisez le modèle d’hébergement in-process.

Pour plus d’informations sur le modèle d’hébergement in-process et sur la configuration du module ASP.NET Core, consultez <xref:host-and-deploy/aspnet-core-module> .

## <a name="configuration-of-iis-with-webconfig"></a>Configuration d’IIS avec web.config

La configuration d’IIS est influencée par la section `<system.webServer>` de *web.config* pour les scénarios IIS qui sont fonctionnels pour les applications ASP.NET Core avec le module ASP.NET Core. Par exemple, la configuration IIS est fonctionnelle pour la compression dynamique. Si IIS est configuré au niveau du serveur pour utiliser la compression dynamique, l’élément `<urlCompression>` dans le fichier *web.config* de l’application peut le désactiver pour une application ASP.NET Core.

Pour plus d'informations, consultez les rubriques suivantes :

* [Référence de configuration pour \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Pour définir des variables d’environnement pour des applications individuelles s’exécutant dans des pools d’applications isolés (pris en charge pour IIS 10,0 ou version ultérieure), consultez la section *commandeAppCmd.exe* de la rubrique [variables \<environmentVariables> d’environnement](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) dans la documentation de référence IIS.

## <a name="configuration-sections-of-webconfig"></a>Sections de configuration de web.config

Les sections de configuration des applications ASP.NET 4.x dans *web.config* ne sont pas utilisées par les applications ASP.NET Core pour la configuration :

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Les applications ASP.NET Core sont configurées à l’aide d’autres fournisseurs de configuration. Pour plus d’informations, consultez [configuration](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pools d'applications

L’isolation des pools d’applications est déterminée par le modèle d’hébergement :

* Hébergement in-process : les applications doivent être exécutées dans des pools d’applications distincts.
* Hébergement hors processus : nous vous recommandons d’isoler les applications les unes des autres en exécutant chaque application dans son propre pool d’applications.

La boîte de dialogue **Ajouter un site web** d’IIS applique un seul pool d’applications par application par défaut. Quand un **Nom du site** est fourni, le texte est automatiquement transféré vers la zone de texte **Pool d’applications** . Un nouveau pool d’applications est créé avec le nom du site une fois qu’il est ajouté.

## <a name="application-pool-no-locidentity"></a>Pool d’applications Identity

Un compte d’identité du pool d’applications permet à une application de s’exécuter sous un compte unique sans qu’il soit nécessaire de créer et de gérer des domaines ou des comptes locaux. Sur IIS 8.0 ou version ultérieure, le processus Worker d’administration IIS (WAS) crée un compte virtuel avec le nom du nouveau pool d’applications et exécute les processus Worker du pool d’applications sous ce compte par défaut. Dans la console de gestion IIS, sous **Paramètres avancés** pour le pool d’applications, assurez-vous que le **Identity** est configuré pour utiliser **ApplicationPool Identity** :

![Boîte de dialogue Paramètres avancés du pool applications](index/_static/apppool-identity.png)

Le processus de gestion IIS crée un identificateur sécurisé avec le nom du pool d’applications dans le système de sécurité Windows. Les ressources peuvent être sécurisées à l’aide de cette identité. Toutefois, cette identité n’est pas un compte d’utilisateur réel et n’apparaît pas dans la console de gestion d’utilisateurs Windows.

Si le processus Worker IIS a besoin d’un accès élevé à l’application, modifiez la liste de contrôle d’accès (ACL) du répertoire contenant l’application :

1. Ouvrez l’Explorateur Windows et accédez au répertoire.

1. Cliquez avec le bouton droit sur le répertoire et sélectionnez **Propriétés** .

1. Sous l’onglet **Sécurité** , sélectionnez le bouton **Modifier** , puis le bouton **Ajouter** .

1. Sélectionnez le bouton **Emplacements** , puis vérifiez que le système est sélectionné.

1. Entrez `IIS AppPool\{APP POOL NAME}` , où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications, dans **la zone Entrez les noms des objets à sélectionner** . Sélectionnez le bouton **Vérifier les noms** . Pour l’option *DefaultAppPool* , vérifiez les noms à l’aide de `IIS AppPool\DefaultAppPool` . Lorsque le bouton **vérifier les noms** est sélectionné, la valeur `DefaultAppPool` est indiquée dans la zone noms d’objets. Il n’est pas possible d’entrer le nom du pool d’applications directement dans la zone des noms d’objets. Utilisez le `IIS AppPool\{APP POOL NAME}` format, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications, lors de la vérification du nom de l’objet.

   ![Sélectionnez la boîte de dialogue utilisateurs ou groupes pour le dossier d’applications : ajoutez le nom du pool d’applications « DefaultAppPool » à « IIS AppPool\" dans la zone des noms d’objets avant de sélectionner « Vérifier les noms ».](index/_static/select-users-or-groups-1.png)

1. Sélectionnez **OK** .

   ![Sélectionnez la boîte de dialogue utilisateurs ou groupes pour le dossier d’applications : après avoir sélectionné « Vérifier les noms », le nom d’objet « DefaultAppPool » est indiqué dans la zone des noms d’objets.](index/_static/select-users-or-groups-2.png)

1. Les autorisations Lire &amp; exécuter doivent être accordées par défaut. Fournissez des autorisations supplémentaires si nécessaire.

L’accès peut également être octroyé par le biais d’une invite de commandes à l’aide de l’outil **ICACLS** . À l’aide de `DefaultAppPool` comme exemple, la commande suivante est utilisée :

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Pour plus d’informations, consultez la rubrique [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="http2-support"></a>Assistance HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est pris en charge avec ASP.NET Core dans les scénarios de déploiement IIS suivants :

* In-process
  * Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure
  * TLS 1.2 ou connexion ultérieure
* Out-of-process
  * Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure
  * Les connexions au serveur périphérique public utilisent HTTP/2, mais la connexion de proxy inverse pour le [serveur Kestrel](xref:fundamentals/servers/kestrel) utilise HTTP/1.1.
  * TLS 1.2 ou connexion ultérieure

Pour un déploiement in-process lors de l’établissement d’une connexion HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) signale `HTTP/2` . Pour un déploiement hors processus lors de l’établissement d’une connexion HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) signale `HTTP/1.1` .

Pour plus d’informations sur les modèles d’hébergement in-process et out-of-process, consultez <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 est activé par défaut. Les connexions reviennent à HTTP/1.1 si une connexion HTTP/2 n’est pas établie. Pour plus d’informations sur la configuration de HTTP/2 avec les déploiements IIS, consultez [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Requêtes préliminaires CORS

*Cette section s’applique uniquement aux applications ASP.NET Core qui ciblent le .NET Framework.*

Pour une application ASP.NET Core qui cible le .NET Framework, les requêtes OPTIONS ne sont pas transmises à l’application par défaut dans IIS. Pour savoir comment configurer les gestionnaires IIS de l’application dans `web.config` pour passer des demandes d’options, consultez [activer les demandes Cross-origin dans API Web ASP.NET 2 : fonctionnement de cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Module d’initialisation de l’application et délai d’inactivité

Quand ils sont hébergés dans IIS par le Module ASP.NET Core version 2 :

* [Module d’initialisation d’application](#application-initialization-module): le processus [de](#in-process-hosting-model) l’application hébergée dans le processus ou [hors processus](#out-of-process-hosting-model) peut être configuré pour démarrer automatiquement lors du redémarrage d’un processus de travail ou du redémarrage du serveur.
* [Délai d’inactivité](#idle-timeout): l’application hébergée [dans le processus](#in-process-hosting-model) peut être configurée pour ne pas dépasser le délai d’inactivité.

### <a name="application-initialization-module"></a>Module d’initialisation de l’application

*S’applique aux applications hébergées in-process et hors processus.*

[L’Initialisation d’application IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) est une fonctionnalité IIS qui envoie une requête HTTP à l’application lorsque le pool d’applications démarre ou est recyclé. La requête déclenche le démarrage de l’application. Par défaut, IIS émet une requête à l’URL racine de l’application (`/`) pour initialiser l’application (consultez les [ressources supplémentaires](#application-initialization-module-and-idle-timeout-additional-resources) pour plus d’informations sur la configuration).

Vérifiez que la fonctionnalité de rôle Initialisation d’application IIS est activée :

Sur Windows 7 ou systèmes de bureau de version ultérieure lorsque vous utilisez IIS localement :

1. Naviguez jusqu’à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).
1. Ouvrez **Internet Information Services** > **Services World Wide Web** > **Fonctionnalités de développement d’applications** .
1. Cochez la case **Initialisation d’application** .

Sur Windows Server 2008 R2 ou version ultérieure :

1. Ouvrez l’ **Assistant Ajout de rôles et de fonctionnalités** .
1. Dans le panneau **Sélectionnez les services de rôle** , ouvrez le nœud **Développement d’applications** .
1. Cochez la case **Initialisation d’application** .

Utilisez une des approches suivantes pour activer le Module d’initialisation de l’application pour le site :

* Utilisation du Gestionnaire IIS :

  1. Sélectionnez **Pools d’applications** dans le volet **Connexions** .
  1. Cliquez avec le bouton de droite sur le pool d’applications de l’application dans la liste, puis sélectionnez **Paramètres avancés** .
  1. La valeur par défaut **Mode de démarrage** est **OnDemand** . Définissez le **Mode de démarrage** sur **AlwaysRunning** . Sélectionnez **OK** .
  1. Ouvrez le nœud **Sites** dans le panneau **Connexions** .
  1. Cliquez avec le bouton de droite sur l’application et sélectionnez **Gérer le site web** > **Paramètres avancés** .
  1. Le paramètre par défaut **Préchargement activé** est **Faux** . Définissez **Préchargement activé** sur **True** . Sélectionnez **OK** .

* À l’aide de `web.config` , ajoutez l' `<applicationInitialization>` élément avec `doAppInitAfterRestart` `true` la valeur aux `<system.webServer>` éléments dans le fichier web.config'de l’application :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Délai d’inactivité

*S’applique aux applications hébergées in-process.*

Pour empêcher la mise en veille après une période d’inactivité de l’application, définissez le délai d’inactivité du pool d’applications à l’aide du Gestionnaire IIS :

1. Sélectionnez **Pools d’applications** dans le volet **Connexions** .
1. Cliquez avec le bouton de droite sur le pool d’applications de l’application dans la liste, puis sélectionnez **Paramètres avancés** .
1. La valeur par défaut **Délai d’inactivité (minutes)** est **20** minutes. Définissez le **Délai d’inactivité (minutes)** sur **0** (zéro). Sélectionnez **OK** .
1. Recyclez le processus Worker.

Pour empêcher les applications hébergées [hors processus](#out-of-process-hosting-model) d’expirer, utilisez une des approches suivantes :

* Effectuez un test ping de l’application à partir d’un service externe afin de garantir son fonctionnement.
* Si l’application héberge uniquement les services d’arrière-plan, évitez l’hébergement IIS et utilisez un [Service Windows pour héberger l’application ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Module d’initialisation de l’application et ressources supplémentaires du délai d’inactivité

* [Initialisation de l’application IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Initialisation de l' \<applicationInitialization> application ](/iis/configuration/system.webserver/applicationinitialization/).
* [Paramètres du modèle de processus pour un \<processModel> pool d’applications ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Déploiement de ressources pour les administrateurs IIS

* [Documentation IIS](/iis)
* [Bien démarrer avec le Gestionnaire IIS dans IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Déploiement d’applications .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/troubleshoot>
* <xref:index>
* [Site officiel de Microsoft IIS](https://www.iis.net/)
* [Bibliothèque de contenu technique Windows Server](/windows-server/windows-server)
* [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Pour une expérience de tutoriel sur la publication d'une app ASP.NET Core sur un serveur IIS, voir <xref:tutorials/publish-to-iis>.

[Installer le bundle d’hébergement .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Les systèmes d’exploitation pris en charge sont les suivants :

* Windows 7 ou version ultérieure
* Windows Server 2008 R2 ou version ultérieure

Le [serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement WebListener) ne fonctionne pas dans une configuration de proxy inverse avec IIS. Utilisez le [serveur Kestrel](xref:fundamentals/servers/kestrel).

Pour plus d’informations sur l’hébergement dans Azure, consultez <xref:host-and-deploy/azure-apps/index>.

Pour obtenir des conseils de dépannage, consultez <xref:test/troubleshoot>.

## <a name="supported-platforms"></a>Plateformes prises en charge

Les applications publiées pour les déploiements 32 bits (x86) ou 64 bits (x64) sont prises en charge. Déployez une application 32 bits avec un kit SDK .NET Core (x86) de 32 bits, sauf si l’application :

* Nécessite l’espace d’adressage de mémoire virtuelle le plus grand disponible pour une application 64 bits.
* Nécessite la taille de pile IIS la plus grande disponible.
* A des dépendances natives 64 bits.

Utilisez un kit SDK .NET Core 64 bits (x64) pour publier une application 64 bits. Un runtime 64 bits doit être présent sur le système hôte.

## <a name="hosting-models"></a>Modèles d'hébergement

### <a name="in-process-hosting-model"></a>Modèle d’hébergement in-process

En utilisant l’hébergement in-process, une application ASP.NET Core s’exécute dans le même processus que son processus de travail IIS. L’hébergement in-process offre des performances améliorées par rapport à l’hébergement out-of-process, car :

* Les requêtes ne sont pas transmises par proxy sur la carte de bouclage. Une carte de bouclage est une interface réseau qui renvoie le trafic réseau sortant vers le même ordinateur.

IIS s’occupe de la gestion des processus par l’intermédiaire du [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

[Module ASP.net Core](xref:host-and-deploy/aspnet-core-module):

* Effectue l’initialisation de l’application.
  * Charge le [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Appelle `Program.Main`.
* Gère la durée de vie de la requête native IIS.

Le modèle d’hébergement in-process n’est pas pris en charge pour les applications ASP.NET Core qui ciblent le .NET Framework.

Le schéma suivant montre la relation entre IIS, le module ASP.NET Core et une application hébergée dans un processus :

![Module ASP.NET Core dans le scénario d’hébergement in-process](index/_static/ancm-inprocess.png)

Une requête arrive du web au pilote HTTP.sys en mode noyau. Le pilote achemine la requête native vers IIS sur le port configuré du site web, généralement 80 (HTTP) ou 443 (HTTPS). Le module ASP.NET Core reçoit la demande native et la transmet au serveur HTTP IIS ( `IISHttpServer` ). Le serveur HTTP IIS est une implémentation du serveur in-process pour IIS qui convertit la requête native en requête managée.

Une fois que IIS HTTP Server a traité la requête, celle-ci est envoyée (push) dans le pipeline de middleware (intergiciel) d’ASP.NET Core. Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application. La réponse de l’application est retransmise à IIS via le serveur HTTP IIS. IIS envoie la réponse au client qui a initié la demande.

L’hébergement in-process est au choix pour les applications existantes, mais les modèles [dotnet new](/dotnet/core/tools/dotnet-new) sont définis par défaut sur le modèle d’hébergement in-process pour tous les scénarios IIS et IIS Express.

`CreateDefaultBuilder` appelle une instance <xref:Microsoft.AspNetCore.Hosting.Server.IServer> en appelant la méthode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> pour démarrer [CoreCLR](/dotnet/standard/glossary#coreclr) et héberger l’application dans le processus Worker IIS ( *w3wp.exe* ou *iisexpress.exe* ). Les tests de performance indiquent que l’hébergement d’une application.NET Core in-process fournit un débit de requêtes beaucoup plus élevé que l’hébergement de l’application out-of-process et la redirection par le biais d’un proxy des requêtes vers le serveur [Kestrel](xref:fundamentals/servers/kestrel).

### <a name="out-of-process-hosting-model"></a>Modèle d’hébergement out-of-process

Étant donné que ASP.NET Core applications s’exécutent dans un processus distinct du processus de travail IIS, le module ASP.NET Core gère la gestion des processus. Le module démarre le processus pour l’application ASP.NET Core quand la première requête arrive, et il redémarre l’application si elle s’arrête ou se bloque. Il s’agit essentiellement du même comportement que celui des applications s’exécutant in-process, et qui sont gérées par le [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Le schéma suivant illustre la relation entre IIS, le module ASP.NET Core et une application hébergée hors processus :

![Module ASP.NET Core dans le scénario d’hébergement out-of-process](index/_static/ancm-outofprocess.png)

Les requêtes arrivent du web au pilote HTTP.sys en mode noyau. Le pilote route les requêtes vers IIS sur le port configuré du site web, généralement 80 (HTTP) ou 443 (HTTPS). Le module transfère les requêtes à Kestrel sur un port aléatoire pour l’application, qui n’est ni le port 80 ni le port 443.

Le module spécifie le port via une variable d’environnement au démarrage, et l’extension <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> configure le serveur pour qu’il écoute sur `http://localhost:{PORT}`. Des vérifications supplémentaires sont effectuées, et les requêtes qui ne proviennent pas du module sont rejetées. Le module ne prend pas en charge le transfert HTTPS : les requêtes sont donc transférées via HTTP, même si IIS les reçoit via HTTPS.

Dès que Kestrel sélectionne la requête dans le module, celle-ci est envoyée (push) dans le pipeline de middlewares d’ASP.NET Core. Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application. L’intergiciel (middleware) ajouté par l’intégration d’IIS met à jour le schéma, l’adresse IP distante et la base du chemin pour prendre en compte le transfert de la requête à Kestrel. La réponse de l’application est ensuite repassée à IIS, qui la renvoie au client HTTP à l’origine de la requête.

Pour obtenir de l’aide sur la configuration du module ASP.NET Core, consultez <xref:host-and-deploy/aspnet-core-module>.

Pour plus d’informations sur l’hébergement, consultez [Héberger dans ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Configuration de l’application

### <a name="enable-the-iisintegration-components"></a>Activer les composants IISIntegration

Lors de la création d’un ordinateur hôte dans `CreateWebHostBuilder` ( *Program.cs* ), appelez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> pour activer l’intégration d’IIS :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Pour plus d'informations sur le `CreateDefaultBuilder`, consultez <xref:fundamentals/host/web-host#set-up-a-host>.

### <a name="iis-options"></a>Options IIS

**Modèle d’hébergement in-process**

Pour configurer les options IIS Server, incluez une configuration de service pour <xref:Microsoft.AspNetCore.Builder.IISServerOptions> dans <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>. L’exemple suivant désactive AutomaticAuthentication :

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Option                         | Default | Paramètre |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si `true`, IIS Server définit le `HttpContext.User` authentifié par [Authentification Windows](xref:security/authentication/windowsauth). Si `false`, le serveur fournit uniquement une identité pour `HttpContext.User` et répond aux questions explicitement posées par `AuthenticationScheme`. L’authentification Windows doit être activée dans IIS pour que `AutomaticAuthentication` fonctionne. Pour plus d’informations, consultez [authentification Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Définit le nom d’affichage montré aux utilisateurs sur les pages de connexion. |

**Modèle d’hébergement out-of-process**

Pour configurer les options IIS, incluez une configuration de service pour <xref:Microsoft.AspNetCore.Builder.IISOptions> dans <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. L’exemple suivant empêche l’application d’être renseignée `HttpContext.Connection.ClientCertificate` :

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

Le [middleware d’intégration IIS](#enable-the-iisintegration-components), qui configure le middleware des en-têtes transférés, et le module ASP.NET Core sont configurés pour transférer le schéma (HTTP/HTTPS) et l’adresse IP distante d’où provient la requête. Une configuration supplémentaire peut être nécessaire pour les applications hébergées derrière des serveurs proxy et des équilibreurs de charge supplémentaires. Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>fichier web.config

Le fichier *web.config* configure le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module). La création, la transformation et la publication du fichier *web.config* sont gérées par une cible MSBuild (`_TransformWebConfig`) quand le projet est publié. Cette cible est présente dans les cibles du SDK web (`Microsoft.NET.Sdk.Web`). Le SDK est défini en haut du fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si le projet ne contient pas de fichier *web.config* , le fichier est créé avec le *processPath* et les *arguments* corrects pour configurer le Module ASP.NET Core, puis il est déplacé vers la [sortie publiée](xref:host-and-deploy/directory-structure).

Si un fichier *web.config* se trouve dans le projet, il est transformé avec le *processPath* et les *arguments* corrects pour configurer le Module ASP.NET Core, puis il est déplacé vers la sortie publiée. La transformation ne modifie pas les paramètres de configuration IIS dans le fichier.

Le fichier *web.config* peut fournir des paramètres de configuration IIS supplémentaires qui contrôlent les modules IIS actifs. Pour plus d’informations sur les modules IIS capables de traiter les requêtes avec des applications ASP.NET Core, consultez la rubrique [Modules IIS](xref:host-and-deploy/iis/modules).

Pour empêcher le kit de développement logiciel (SDK) Web de transformer le fichier *web.config* , utilisez la **\<IsTransformWebConfigDisabled>** propriété dans le fichier projet :

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Lorsque vous désactivez le Kit de développement logiciel (SDK) Web en transformant le fichier, le *processPath* et les *arguments* doivent être définis manuellement par le développeur. Pour plus d'informations, consultez <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>emplacement du fichier web.config

Pour configurer correctement le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) , le fichier *web.config* doit être présent au chemin d’accès [racine du contenu](xref:fundamentals/index#content-root) (en général, le chemin d’accès de base de l’application) de l’application déployée. Il s’agit du même emplacement que le chemin physique du site Web fourni à IIS. Le fichier *web.config* est nécessaire à la racine de l’application pour permettre la publication de plusieurs applications à l’aide de Web Deploy.

Des fichiers sensibles existent sur le chemin d’accès physique de l’application, par exemple *\<assembly>.runtimeconfig.jssur* , *\<assembly> . xml* (commentaires de documentation XML) et *\<assembly>.deps.jssur* . Lorsque le fichier *web.config* est présent et que le site démarre normalement, IIS ne traite pas ces fichiers sensibles s’ils sont demandés. Si le fichier *web.config* est absent, nommé de manière incorrecte ou s’il est incapable de configurer le site pour un démarrage normal, IIS peut traiter des fichiers sensibles publiquement.

**Le fichier *web.config* doit être présent dans le déploiement à tout moment, correctement nommé et capable de configurer le site pour un démarrage normal. Ne supprimez jamais le fichier *web.config* d’un déploiement de production.**

### <a name="transform-webconfig"></a>Transformer web.config

Si vous devez transformer *web.config* lors de la publication (par exemple, définir les variables d’environnement basées sur la configuration, le profil ou l’environnement), consultez <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Configuration IIS

**Systèmes d’exploitation Windows Server**

Activez le rôle serveur **Serveur Web (IIS)** et établissez des services de rôle.

1. Utilisez l’Assistant **Ajouter des rôles et des fonctionnalités** par le biais du menu **Gérer** ou du lien dans **Gestionnaire de serveur** . À l’étape **Rôles de serveurs** , cochez la case **Serveur Web (IIS)** .

   ![Le rôle Serveur Web IIS est sélectionné à l’étape Sélectionner des rôles de serveurs.](index/_static/server-roles-ws2016.png)

1. Après l’étape **Fonctionnalités** , l’étape **Services de rôle** se charge pour le serveur Web (IIS). Sélectionnez les services de rôle IIS souhaités ou acceptez les services de rôle par défaut fournis.

   ![Les services de rôle par défaut sont sélectionnés à l’étape Sélectionner des services de rôle.](index/_static/role-services-ws2016.png)

   **Authentification Windows (facultatif)**  
   Pour activer l’authentification Windows, développez les nœuds suivants : sécurité du **serveur Web**  >  **Security** . Sélectionnez la fonctionnalité **Authentification Windows** . Pour plus d’informations, consultez [authentification \<windowsAuthentication> Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [configurer l’authentification Windows](xref:security/authentication/windowsauth).

   **WebSockets (facultatif)**  
   WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure. Pour activer les WebSockets, développez les nœuds suivants : développement d’applications de **serveur Web**  >  **Application Development** . Sélectionnez la fonctionnalité **Protocole WebSocket** . Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).

1. Validez l’étape de **Confirmation** pour installer les services et le rôle de serveur web. Un redémarrage du serveur/d’IIS n’est pas nécessaire après l’installation du rôle **Serveur Web (IIS)** .

**Systèmes d’exploitation Windows Desktop**

Activez la **Console de gestion IIS** et les **Services World Wide Web** .

1. Naviguez jusqu’à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).

1. Ouvrez le nœud **Internet Information Services** . Ouvrez le nœud **Outils de gestion Web** .

1. Cochez la case **Console de gestion IIS** .

1. Cochez la case **Services World Wide Web** .

1. Acceptez les fonctionnalités par défaut pour **Services World Wide Web** ou personnalisez les fonctionnalités d’IIS.

   **Authentification Windows (facultatif)**  
   Pour activer l’authentification Windows, développez les nœuds suivants : **World Wide webing services**  >  **Security** . Sélectionnez la fonctionnalité **Authentification Windows** . Pour plus d’informations, consultez [authentification \<windowsAuthentication> Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [configurer l’authentification Windows](xref:security/authentication/windowsauth).

   **WebSockets (facultatif)**  
   WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure. Pour activer WebSockets, développez les nœuds suivants : fonctionnalités de développement d’applications **World Wide Web services**  >  **Application Development Features** . Sélectionnez la fonctionnalité **Protocole WebSocket** . Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).

1. Si l’installation d’IIS requiert un redémarrage, redémarrez le système.

![Console de gestion IIS et Services World Wide Web sont sélectionnés dans Fonctionnalités de Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Installer le bundle d’hébergement .NET Core

Installez le *bundle d’hébergement .NET Core* sur le système hôte. L’offre groupée installe le Runtime .NET Core, la bibliothèque .NET Core et le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module). Le module permet aux applications ASP.NET Core de s’exécuter derrière IIS.

> [!IMPORTANT]
> Si le bundle d’hébergement est installé avant IIS, l’installation du bundle doit être réparée. Après avoir installé IIS, réexécutez le programme d’installation du bundle d’hébergement.
>
> Si le bundle d’hébergement est installé après l’installation de la version 64 bits (x 64) de .NET Core, les SDK peuvent apparaître manquants ([Aucun SDK .NET Core n’a été détecté](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Pour résoudre le problème, consultez <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="download"></a>Télécharger

1. Accédez à la page [Télécharger .net Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Sélectionnez la version .NET Core de votre choix.
1. Dans la colonne **Run apps - Runtime** , recherchez la ligne de la version du runtime .NET Core souhaitée.
1. Téléchargez le programme d’installation à l’aide du lien d' **hébergement** .

> [!WARNING]
> Certains programmes d’installation contiennent des versions qui sont arrivées à leur fin de vie (EOL) et qui ne sont plus prises en charge par Microsoft. Pour plus d’informations, consultez la [politique de support](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Installer le bundle d’hébergement

1. Exécutez le programme d’installation sur le serveur. Les paramètres suivants sont disponibles lorsque vous exécutez le programme d’installation à partir d’un shell de commande administrateur :

   * `OPT_NO_ANCM=1`: Ignorez l’installation du module ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Ignorez l’installation du Runtime .NET Core. Utilisé lorsque le serveur héberge uniquement [des déploiements autonomes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Ignorez l’installation du Framework partagé ASP.NET (runtime ASP.NET). Utilisé lorsque le serveur héberge uniquement [des déploiements autonomes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Ignorez l’installation des runtimes x86. Utilisez ce paramètre lorsque vous savez que vous n’hébergerez pas d’applications 32 bits. Si vous n’excluez pas d’avoir à héberger des applications 32 bits et 64 bits dans le futur, n'utilisez pas ce paramètre et installez les deux runtimes.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Désactive la vérification de l’utilisation d’une configuration partagée IIS lorsque la configuration partagée ( *applicationHost.config* ) se trouve sur le même ordinateur que l’installation d’IIS. *Disponible uniquement pour les programmes d’installation du pack d’hébergement ASP.NET Core 2.2 ou version ultérieure.* Pour plus d'informations, consultez <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Redémarrez le système ou exécutez les commandes suivantes dans une interface de commande :

   ```console
   net stop was /y
   net start w3svc
   ```
   Le redémarrage d’IIS récupère une modification apportée au CHEMIN D’ACCÈS du système, qui est une variable d’environnement, par le programme d’installation.

Il n’est pas nécessaire d’arrêter manuellement des sites individuels dans IIS lors de l’installation du bundle d’hébergement. Les applications hébergées (sites IIS) redémarrent lors du redémarrage d’IIS. Les applications redémarrent lorsqu’elles reçoivent leur première requête, y compris à partir du [module d’initialisation](#application-initialization-module-and-idle-timeout)de l’application.

ASP.NET Core adopte le comportement de restauration par progression pour les mises à jour correctives des packages d’infrastructure partagés. Lorsque les applications hébergées par IIS redémarrent avec IIS, elles sont chargées avec les dernières versions de correctif de leurs packages référencés lorsqu’elles reçoivent leur première demande. Si IIS n’est pas redémarré, les applications redémarrent et présentent le comportement de restauration par progression lorsque leurs processus de travail sont recyclés et qu’ils reçoivent leur première demande.

> [!NOTE]
> Pour plus d’informations sur la configuration partagée IIS, consultez [Module ASP.NET Core avec configuration partagée des services Internet (IIS)](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Installer Web Deploy lors de la publication avec Visual Studio

Quand vous déployez des applications sur un serveur avec [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), installez la dernière version de Web Deploy sur le serveur. Pour installer Web Deploy, utilisez [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) ou obtenez un programme d’installation directement à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). La méthode recommandée est d’utiliser WebPI. WebPI offre une installation autonome et une configuration pour les fournisseurs d’hébergement.

## <a name="create-the-iis-site"></a>Créer le site IIS

1. Sur le système d’hébergement, créez un dossier pour contenir les fichiers et dossiers publiés de l’application. À l’étape suivante, le chemin du dossier est fourni à IIS en tant que chemin d’accès physique à l’application. Pour plus d’informations sur le dossier de déploiement et la disposition d’un fichier d’une application, consultez <xref:host-and-deploy/directory-structure>.

1. Dans le gestionnaire des services Internet, ouvrez le nœud du serveur dans le panneau **connexions** . Cliquez avec le bouton de droite sur le dossier **Sites** . Sélectionnez **Ajouter un site Web** dans le menu contextuel.

1. Spécifiez le **Nom du site** et définissez le **Chemin physique** sur le dossier de déploiement de l’application. Fournissez la configuration de **liaison** et créez le site Web en sélectionnant **OK** :

   ![Spécifiez le nom du site, le chemin physique et le nom d’hôte à l’étape Ajouter un site Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Les liaisons génériques de niveau supérieur (`http://*:80/` et `http://+:80`) ne doivent **pas** être utilisées. Les liaisons génériques de niveau supérieur peuvent exposer votre application à des failles de sécurité. Cela s’applique aux caractères génériques forts et faibles. Utilisez des noms d’hôte explicites plutôt que des caractères génériques. Une liaison générique de sous-domaine (par exemple, `*.mysub.com`) ne présente pas ce risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable). Consultez la [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pour plus d’informations.

1. Sous le nœud du serveur, sélectionnez **Pools d’applications** .

1. Cliquez avec le bouton de droite sur le pool d’applications du site et sélectionnez **Paramètres de base** dans le menu contextuel.

1. Dans la fenêtre **Modifier le pool d’applications** , définissez la **version .NET CLR** sur **Aucun code managé**  :

   ![Définissez Aucun code managé pour la version CLR .NET.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core s’exécute dans un processus séparé et gère l’exécution. ASP.NET Core ne repose pas sur le chargement de Desktop CLR (CLR .NET)&mdash;la prise en charge du Common Language Runtime Core (CoreCLR) pour .NET Core est démarrée pour héberger l’application dans le processus Worker. La configuration de la **version CLR .NET** sur **Aucun code managé** est facultative, mais recommandée.

1. *ASP.NET Core 2.2 ou version ultérieure*  : pour un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) 64 bits (x64) qui utilise le [modèle d’hébergement In-process](#in-process-hosting-model), désactivez le pool d’applications pour les processus 32 bits (x86).

   Dans la barre latérale **Actions** du gestionnaire IIS > **Pools d’applications** , sélectionnez **Définir les valeurs par défaut du pool d’applications** ou **Paramètres avancés** . Recherchez **Activer les applications 32 bits** et définissez la valeur sur `False`. Ce paramètre n’affecte pas les applications déployées pour l’[hébergement Out-of-process](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Vérifiez que l’identité de modèle de processus dispose des autorisations appropriées.

   Si l’identité par défaut du pool d’applications ( **modèle de processus**  >  **Identity** ) est **Identity** remplacée par une autre identité, vérifiez que la nouvelle identité dispose des autorisations nécessaires pour accéder au dossier de l’application, à la base de données et à d’autres ressources requises. Par exemple, le pool d’applications nécessite l’accès en lecture et en écriture aux dossiers dans lesquels l’application lit et écrit des fichiers.

**Configuration de l’authentification Windows (facultatif)**  
Pour plus d'informations, consultez la rubrique [Configurer l’authentification Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Déployer l’application

Déployez l’application dans le dossier **Chemin d’accès physique** IIS qui a été créé dans la section [Créer le site IIS](#create-the-iis-site). [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) est le mécanisme recommandé pour le déploiement, mais il existe plusieurs options pour déplacer l’application à partir du dossier *publier* du projet vers le dossier de déploiement du système d’hébergement.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy avec Visual Studio

Pour découvrir comment créer un profil de publication pour une utilisation avec Web Deploy, consultez la rubrique [Profils de publication Visual Studio pour le déploiement d’applications ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles). Si le fournisseur d’hébergement fournit un profil de publication ou prend en charge sa création, téléchargez son profil et importez-le à l’aide de la boîte de dialogue **Publier** de Visual Studio :

![Boîte de dialogue Publier](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy en dehors de Visual Studio

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) peut également être utilisé en dehors de Visual Studio à partir de la ligne de commande. Pour plus d’informations, consultez [Web Deployment Tool (Outil de déploiement Web)](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatives à Web Deploy

Utilisez la méthode de votre choix, telle que la copie manuelle, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) ou [PowerShell](/powershell/), pour déplacer l’application vers le système d’hébergement.

Pour plus d’informations sur le déploiement d’ASP.NET Core sur IIS, consultez la section [Déploiement de ressources pour les administrateurs IIS](#deployment-resources-for-iis-administrators).

## <a name="browse-the-website"></a>Parcourir le site Web

Une fois que l’application est déployée sur le système hôte, envoyez une requête à l’un des points de terminaison publics de l’application.

Dans l’exemple suivant, le site est lié à un **nom d’hôte** IIS de `www.mysite.com` sur le **Port** `80`. Une demande est faite à `http://www.mysite.com` :

![Le navigateur Microsoft Edge a chargé la page de démarrage IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Fichiers de déploiement verrouillés

Les fichiers dans le dossier de déploiement sont verrouillés quand l’application est en cours d’exécution. Les fichiers verrouillés ne peuvent pas être remplacés au cours du déploiement. Pour libérer des fichiers verrouillés dans un déploiement, arrêtez le pool d’applications à l’aide **d’une** des approches suivantes :

* Utilisez Web Deploy et référencez `Microsoft.NET.Sdk.Web` dans le fichier projet. Un fichier *app_offline.htm* est placé à la racine du répertoire de l’application web. Quand le fichier est présent, le module ASP.NET Core ferme l’application normalement et sert le fichier *app_offline.htm* pendant le déploiement. Pour plus d’informations, consultez les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Arrêtez manuellement le pool d’applications dans le Gestionnaire IIS sur le serveur.
* Utilisez PowerShell pour supprimer des *app_offline.htm* (nécessite PowerShell 5 ou version ultérieure) :

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Protection de données

La [pile de protection des données ASP.NET Core](xref:security/data-protection/introduction) est utilisée par plusieurs [intergiciels (middlewares)](xref:fundamentals/middleware/index) ASP.NET Core, y compris l’intergiciel utilisé dans l’authentification. Même si les API de protection des données ne sont pas appelées par le code de l’utilisateur, la protection des données doit être configurée avec un script de déploiement ou dans un code utilisateur pour créer un [magasin de clés](xref:security/data-protection/implementation/key-management) de chiffrement persistantes. Si la protection des données n’est pas configurée, les clés sont conservées en mémoire et ignorées au redémarrage de l’application.

Si le Key Ring est stocké en mémoire, au redémarrage de l’application :

* Tous les cookie jetons d’authentification de base sont invalidés. 
* Les utilisateurs doivent se reconnecter pour envoyer leur prochaine demande. 
* toutes les données protégées par le Key Ring ne peuvent plus être déchiffrées. Cela peut inclure des [jetons CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) et des ASP.NET Core les de la [MVC cookie ](xref:fundamentals/app-state#tempdata).

Pour configurer la protection des données sous IIS afin de rendre persistante le Key Ring, adoptez **l’une** des approches suivantes :

* **Créer des clés de Registre de la protection des données**

  Les clés de la protection des données utilisées par les applications ASP.NET Core sont stockées dans le registre externe aux applications. Afin de conserver les clés pour une application donnée, créez des clés de Registre pour le pool d’applications.

  Pour les installations IIS autonomes hors d’une batterie de serveurs, le [script PowerShell Provision-AutoGenKeys.ps1 de protection des données](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) peut être utilisé pour chaque pool d’applications utilisé avec une application ASP.NET Core. Ce script crée une clé de Registre dans le Registre HKLM, accessible uniquement pour le compte du processus Worker du pool d’applications de l’application. Les clés sont chiffrées au repos à l’aide de DPAPI avec une clé à l’échelle de la machine.

  Dans les scénarios de batterie de serveurs Web, vous pouvez configurer une application afin qu’elle utilise un chemin UNC pour stocker son Key Ring de protection des données. Par défaut, les clés de protection des données ne sont pas chiffrées. Vérifiez que les autorisations de fichiers pour le partage réseau sont limitées au compte Windows sous lequel s’exécute l’application. Un certificat X509 peut être utilisé pour protéger les clés au repos. Mettez en œuvre un mécanisme permettant aux utilisateurs de charger des certificats : placez les certificats dans le magasin de certificats approuvés de l’utilisateur et vérifiez qu’ils sont disponibles sur toutes les machines où s’exécute l’application de l’utilisateur. Pour plus d’informations, consultez [Configurer la protection des données ASP.NET Core](xref:security/data-protection/configuration/overview).

* **Configurer le pool d’applications IIS pour charger le profil utilisateur**

  Ce paramètre se trouve dans la section **Modèle de processus** sous les **Paramètres avancés** du pool d’applications. Affectez la valeur `True` à **Charger le profil utilisateur** . Lorsqu’elle est définie sur `True`, les clés sont stockées dans le répertoire du profil utilisateur, protégées à l’aide de DPAPI avec une clé propre au compte d’utilisateur utilisé pour le pool d’applications. Les clés sont persistantes dans le dossier *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* .

  L’[attribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) du pool d’applications doit également être activé. La valeur par défaut de `setProfileEnvironment` est `true`. Dans certains scénarios (par exemple pour le système d’exploitation Windows), `setProfileEnvironment` est défini sur `false`. Si les clés ne sont pas stockées dans le répertoire de profil utilisateur comme prévu :

  1. Accédez au dossier *%windir%/system32/inetsrv/config* .
  1. Ouvrez le fichier *applicationHost.config* .
  1. Recherchez l’élément `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Confirmez que l’attribut `setProfileEnvironment` n’est pas présent, ce qui implique que la valeur par défaut est `true`, ou définissez de manière explicite la valeur de l’attribut sur `true`.

* **Utiliser le système de fichiers comme magasin de Key Ring**

  Ajustez le code d’application pour [utiliser le système de fichiers comme magasin de porte-clés](xref:security/data-protection/configuration/overview). Utilisez un certificat X509 pour protéger le Key Ring et vérifiez qu’il s’agit d’un certificat approuvé. S’il s’agit d’un certificat auto-signé, placez-le dans le magasin Racine approuvée.

  Quand vous utilisez IIS dans une batterie de serveurs web :

  * Utilisez un partage de fichiers accessible par tous les ordinateurs.
  * Déployez un certificat X509 sur chaque ordinateur. Configurez la [protection des données dans le code](xref:security/data-protection/configuration/overview).

* **Définir une stratégie au niveau de l’ordinateur pour la protection des données**

  Le système de protection des données offre une prise en charge limitée de la définition d’une [stratégie au niveau de l’ordinateur](xref:security/data-protection/configuration/machine-wide-policy) par défaut pour toutes les applications qui utilisent les API de protection des données. Pour plus d'informations, consultez <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Répertoires virtuels

Les [répertoires virtuels IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ne sont pas pris en charge avec les applications ASP.NET Core. Une application peut être hébergée en tant que [sous-application](#sub-applications).

## <a name="sub-applications"></a>Sous-applications

Une application ASP.NET Core peut être hébergée en tant que [sous-application IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Le chemin d'accès de la sous-application devient partie intégrante de l’URL de l’application racine.

Les liens de ressources statiques au sein de la sous-application doivent utiliser la notation tilde-barre oblique (`~/`). La notation tilde-barre oblique déclenche un [Tag Helper](xref:mvc/views/tag-helpers/intro) afin d’ajouter l’élément pathbase de la sous-application au lien relatif rendu. Pour une sous-application dans `/subapp_path`, une image liée à `src="~/image.png"` est restituée sous la forme `src="/subapp_path/image.png"`. Le composant Static File Middleware de l’application racine ne traite la demande de fichiers statiques. La demande est traitée par le composant Static File Middleware de la sous-application.

Si l’attribut `src` d’une ressource statique est défini sur un chemin d’accès absolu (par exemple, `src="/image.png"`), le lien apparaît sans l’élément pathbase de la sous-application. Le composant Static File Middleware de l’application racine tente de traiter la ressource à partir de l’élément [webroot](xref:fundamentals/index#web-root) de l’application racine, ce qui entraîne une erreur *404 - Introuvable* , sauf si la ressource statique est disponible depuis l’application racine.

Pour héberger une application ASP.NET Core en tant que sous-application d’une autre application ASP.NET Core :

1. Établissez un pool d’applications pour la sous-application. Définissez la **version CLR .NET** sur **Aucun code managé** car la prise en charge du Common Language Runtime Core (CoreCLR) pour Core .Net est démarrée pour héberger l’application dans le processus Worker et non dans le Desktop CLR (CLR .Net).

1. Ajoutez le site racine dans IIS Manager en plaçant la sous-application dans un dossier du site racine.

1. Cliquez avec le bouton droit sur le dossier de la sous-application dans IIS Manager, puis sélectionnez **Convertir en application** .

1. Dans la boîte de dialogue **Ajouter une application** , utilisez le bouton **Sélectionner** de l’option **Pool d’applications** pour affecter le pool d’applications que vous avez créé pour la sous-application. Sélectionnez **OK** .

L’attribution d’un pool d’applications distinct à la sous-application est obligatoire lorsque vous utilisez le modèle d’hébergement in-process.

Pour plus d’informations sur le modèle d’hébergement in-process et sur la configuration du module ASP.NET Core, consultez <xref:host-and-deploy/aspnet-core-module> .

## <a name="configuration-of-iis-with-webconfig"></a>Configuration d’IIS avec web.config

La configuration d’IIS est influencée par la section `<system.webServer>` de *web.config* pour les scénarios IIS qui sont fonctionnels pour les applications ASP.NET Core avec le module ASP.NET Core. Par exemple, la configuration IIS est fonctionnelle pour la compression dynamique. Si IIS est configuré au niveau du serveur pour utiliser la compression dynamique, l’élément `<urlCompression>` dans le fichier *web.config* de l’application peut le désactiver pour une application ASP.NET Core.

Pour plus d'informations, consultez les rubriques suivantes :

* [Référence de configuration pour \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Pour définir des variables d’environnement pour des applications individuelles s’exécutant dans des pools d’applications isolés (pris en charge pour IIS 10,0 ou version ultérieure), consultez la section *commandeAppCmd.exe* de la rubrique [variables \<environmentVariables> d’environnement](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) dans la documentation de référence IIS.

## <a name="configuration-sections-of-webconfig"></a>Sections de configuration de web.config

Les sections de configuration des applications ASP.NET 4.x dans *web.config* ne sont pas utilisées par les applications ASP.NET Core pour la configuration :

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Les applications ASP.NET Core sont configurées à l’aide d’autres fournisseurs de configuration. Pour plus d’informations, consultez [configuration](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pools d'applications

L’isolation des pools d’applications est déterminée par le modèle d’hébergement :

* Hébergement in-process : les applications doivent être exécutées dans des pools d’applications distincts.
* Hébergement hors processus : nous vous recommandons d’isoler les applications les unes des autres en exécutant chaque application dans son propre pool d’applications.

La boîte de dialogue **Ajouter un site web** d’IIS applique un seul pool d’applications par application par défaut. Quand un **Nom du site** est fourni, le texte est automatiquement transféré vers la zone de texte **Pool d’applications** . Un nouveau pool d’applications est créé avec le nom du site une fois qu’il est ajouté.

## <a name="application-pool-no-locidentity"></a>Pool d’applications Identity

Un compte d’identité du pool d’applications permet à une application de s’exécuter sous un compte unique sans qu’il soit nécessaire de créer et de gérer des domaines ou des comptes locaux. Sur IIS 8.0 ou version ultérieure, le processus Worker d’administration IIS (WAS) crée un compte virtuel avec le nom du nouveau pool d’applications et exécute les processus Worker du pool d’applications sous ce compte par défaut. Dans la console de gestion IIS, sous **Paramètres avancés** pour le pool d’applications, assurez-vous que le **Identity** est configuré pour utiliser **ApplicationPool Identity** :

![Boîte de dialogue Paramètres avancés du pool applications](index/_static/apppool-identity.png)

Le processus de gestion IIS crée un identificateur sécurisé avec le nom du pool d’applications dans le système de sécurité Windows. Les ressources peuvent être sécurisées à l’aide de cette identité. Toutefois, cette identité n’est pas un compte d’utilisateur réel et n’apparaît pas dans la console de gestion d’utilisateurs Windows.

Si le processus Worker IIS a besoin d’un accès élevé à l’application, modifiez la liste de contrôle d’accès (ACL) du répertoire contenant l’application :

1. Ouvrez l’Explorateur Windows et accédez au répertoire.

1. Cliquez avec le bouton droit sur le répertoire et sélectionnez **Propriétés** .

1. Sous l’onglet **Sécurité** , sélectionnez le bouton **Modifier** , puis le bouton **Ajouter** .

1. Sélectionnez le bouton **Emplacements** , puis vérifiez que le système est sélectionné.

1. Entrez **IIS AppPool\\<app_pool_name>** dans la zone **Entrer les noms des objets à sélectionner** . Sélectionnez le bouton **Vérifier les noms** . Pour le *DefaultAppPool* , vérifiez les noms à l’aide de **IIS AppPool\DefaultAppPool** . Lorsque le bouton **Vérifier les noms** est sélectionné, une valeur de **DefaultAppPool** est indiquée dans la zone des noms d’objets. Il n’est pas possible d’entrer le nom du pool d’applications directement dans la zone des noms d’objets. Utilisez le format **IIS AppPool\\<app_pool_name>** lors de la vérification du nom de l’objet.

   ![Sélectionnez la boîte de dialogue utilisateurs ou groupes pour le dossier d’applications : ajoutez le nom du pool d’applications « DefaultAppPool » à « IIS AppPool\" dans la zone des noms d’objets avant de sélectionner « Vérifier les noms ».](index/_static/select-users-or-groups-1.png)

1. Sélectionnez **OK** .

   ![Sélectionnez la boîte de dialogue utilisateurs ou groupes pour le dossier d’applications : après avoir sélectionné « Vérifier les noms », le nom d’objet « DefaultAppPool » est indiqué dans la zone des noms d’objets.](index/_static/select-users-or-groups-2.png)

1. Les autorisations Lire &amp; exécuter doivent être accordées par défaut. Fournissez des autorisations supplémentaires si nécessaire.

L’accès peut également être octroyé par le biais d’une invite de commandes à l’aide de l’outil **ICACLS** . À l’aide de *DefaultAppPool* en exemple, la commande suivante est utilisée :

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Pour plus d’informations, consultez la rubrique [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="http2-support"></a>Assistance HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est pris en charge avec ASP.NET Core dans les scénarios de déploiement IIS suivants :

* In-process
  * Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure
  * TLS 1.2 ou connexion ultérieure
* Out-of-process
  * Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure
  * Les connexions au serveur périphérique public utilisent HTTP/2, mais la connexion de proxy inverse pour le [serveur Kestrel](xref:fundamentals/servers/kestrel) utilise HTTP/1.1.
  * TLS 1.2 ou connexion ultérieure

Pour un déploiement in-process lorsqu’une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/2`. Pour un déploiement in-process lorsqu’une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/1.1`.

Pour plus d’informations sur les modèles d’hébergement in-process et out-of-process, consultez <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 est activé par défaut. Les connexions reviennent à HTTP/1.1 si une connexion HTTP/2 n’est pas établie. Pour plus d’informations sur la configuration de HTTP/2 avec les déploiements IIS, consultez [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Requêtes préliminaires CORS

*Cette section s’applique uniquement aux applications ASP.NET Core qui ciblent le .NET Framework.*

Pour une application ASP.NET Core qui cible le .NET Framework, les requêtes OPTIONS ne sont pas transmises à l’application par défaut dans IIS. Pour savoir comment configurer les gestionnaires IIS de l’application dans *web.config* pour passer des demandes d’options, consultez [activer les demandes Cross-Origin dans API Web ASP.NET 2 : fonctionnement de cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Module d’initialisation de l’application et délai d’inactivité

Quand ils sont hébergés dans IIS par le Module ASP.NET Core version 2 :

* [Module d’initialisation d’application](#application-initialization-module): le processus [de](#in-process-hosting-model) l’application hébergée dans le processus ou [hors processus](#out-of-process-hosting-model) peut être configuré pour démarrer automatiquement lors du redémarrage d’un processus de travail ou du redémarrage du serveur.
* [Délai d’inactivité](#idle-timeout): l’application hébergée [dans le processus](#in-process-hosting-model) peut être configurée pour ne pas dépasser le délai d’inactivité.

### <a name="application-initialization-module"></a>Module d’initialisation de l’application

*S’applique aux applications hébergées in-process et hors processus.*

[L’Initialisation d’application IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) est une fonctionnalité IIS qui envoie une requête HTTP à l’application lorsque le pool d’applications démarre ou est recyclé. La requête déclenche le démarrage de l’application. Par défaut, IIS émet une requête à l’URL racine de l’application (`/`) pour initialiser l’application (consultez les [ressources supplémentaires](#application-initialization-module-and-idle-timeout-additional-resources) pour plus d’informations sur la configuration).

Vérifiez que la fonctionnalité de rôle Initialisation d’application IIS est activée :

Sur Windows 7 ou systèmes de bureau de version ultérieure lorsque vous utilisez IIS localement :

1. Naviguez jusqu’à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).
1. Ouvrez **Internet Information Services** > **Services World Wide Web** > **Fonctionnalités de développement d’applications** .
1. Cochez la case **Initialisation d’application** .

Sur Windows Server 2008 R2 ou version ultérieure :

1. Ouvrez l’ **Assistant Ajout de rôles et de fonctionnalités** .
1. Dans le panneau **Sélectionnez les services de rôle** , ouvrez le nœud **Développement d’applications** .
1. Cochez la case **Initialisation d’application** .

Utilisez une des approches suivantes pour activer le Module d’initialisation de l’application pour le site :

* Utilisation du Gestionnaire IIS :

  1. Sélectionnez **Pools d’applications** dans le volet **Connexions** .
  1. Cliquez avec le bouton de droite sur le pool d’applications de l’application dans la liste, puis sélectionnez **Paramètres avancés** .
  1. La valeur par défaut **Mode de démarrage** est **OnDemand** . Définissez le **Mode de démarrage** sur **AlwaysRunning** . Sélectionnez **OK** .
  1. Ouvrez le nœud **Sites** dans le panneau **Connexions** .
  1. Cliquez avec le bouton de droite sur l’application et sélectionnez **Gérer le site web** > **Paramètres avancés** .
  1. Le paramètre par défaut **Préchargement activé** est **Faux** . Définissez **Préchargement activé** sur **True** . Sélectionnez **OK** .

* À l’aide de *web.config* , ajoutez l’élément `<applicationInitialization>` avec `doAppInitAfterRestart` défini sur `true` aux éléments `<system.webServer>` dans le fichier *web.config* de l’application :

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Délai d’inactivité

*S’applique aux applications hébergées in-process.*

Pour empêcher la mise en veille après une période d’inactivité de l’application, définissez le délai d’inactivité du pool d’applications à l’aide du Gestionnaire IIS :

1. Sélectionnez **Pools d’applications** dans le volet **Connexions** .
1. Cliquez avec le bouton de droite sur le pool d’applications de l’application dans la liste, puis sélectionnez **Paramètres avancés** .
1. La valeur par défaut **Délai d’inactivité (minutes)** est **20** minutes. Définissez le **Délai d’inactivité (minutes)** sur **0** (zéro). Sélectionnez **OK** .
1. Recyclez le processus Worker.

Pour empêcher les applications hébergées [hors processus](#out-of-process-hosting-model) d’expirer, utilisez une des approches suivantes :

* Effectuez un test ping de l’application à partir d’un service externe afin de garantir son fonctionnement.
* Si l’application héberge uniquement les services d’arrière-plan, évitez l’hébergement IIS et utilisez un [Service Windows pour héberger l’application ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Module d’initialisation de l’application et ressources supplémentaires du délai d’inactivité

* [Initialisation de l’application IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Initialisation de l' \<applicationInitialization> application ](/iis/configuration/system.webserver/applicationinitialization/).
* [Paramètres du modèle de processus pour un \<processModel> pool d’applications ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="deployment-resources-for-iis-administrators"></a>Déploiement de ressources pour les administrateurs IIS

* [Documentation IIS](/iis)
* [Bien démarrer avec le Gestionnaire IIS dans IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Déploiement d’applications .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/troubleshoot>
* <xref:index>
* [Site officiel de Microsoft IIS](https://www.iis.net/)
* [Bibliothèque de contenu technique Windows Server](/windows-server/windows-server)
* [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Pour une expérience de tutoriel sur la publication d'une app ASP.NET Core sur un serveur IIS, voir <xref:tutorials/publish-to-iis>.

[Installer le bundle d’hébergement .NET Core](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Les systèmes d’exploitation pris en charge sont les suivants :

* Windows 7 ou version ultérieure
* Windows Server 2008 R2 ou version ultérieure

Le [serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement WebListener) ne fonctionne pas dans une configuration de proxy inverse avec IIS. Utilisez le [serveur Kestrel](xref:fundamentals/servers/kestrel).

Pour plus d’informations sur l’hébergement dans Azure, consultez <xref:host-and-deploy/azure-apps/index>.

Pour obtenir des conseils de dépannage, consultez <xref:test/troubleshoot>.

## <a name="supported-platforms"></a>Plateformes prises en charge

Les applications publiées pour les déploiements 32 bits (x86) ou 64 bits (x64) sont prises en charge. Déployez une application 32 bits avec un kit SDK .NET Core (x86) de 32 bits, sauf si l’application :

* Nécessite l’espace d’adressage de mémoire virtuelle le plus grand disponible pour une application 64 bits.
* Nécessite la taille de pile IIS la plus grande disponible.
* A des dépendances natives 64 bits.

Utilisez un kit SDK .NET Core 64 bits (x64) pour publier une application 64 bits. Un runtime 64 bits doit être présent sur le système hôte.

ASP.NET Core est livré avec le [serveur Kestrel](xref:fundamentals/servers/kestrel), un serveur HTTP multiplateforme par défaut.

Lorsqu’ [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) ou [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) sont utilisés, l’application s’exécute dans un processus séparé du processus Worker IIS (le mode d’hébergement *out-of-process* ) avec le [serveur Kestrel](xref:fundamentals/servers/index#kestrel).

Étant donné que les applications ASP.NET Core s’exécutent dans un processus distinct du processus de travail IIS, le module s’occupe de la gestion du processus. Le module démarre le processus pour l’application ASP.NET Core quand la première requête arrive, et il redémarre l’application si elle s’arrête ou se bloque. Il s’agit essentiellement du même comportement que celui des applications s’exécutant in-process, et qui sont gérées par le [service d’activation des processus Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Le schéma suivant illustre la relation entre IIS, le module ASP.NET Core et une application hébergée hors processus :

![Module ASP.NET Core](index/_static/ancm-outofprocess.png)

Les requêtes arrivent du web au pilote HTTP.sys en mode noyau. Le pilote route les requêtes vers IIS sur le port configuré du site web, généralement 80 (HTTP) ou 443 (HTTPS). Le module transfère les requêtes à Kestrel sur un port aléatoire pour l’application, qui n’est ni le port 80 ni le port 443.

Le module spécifie le port via une variable d’environnement au démarrage, et l' [intergiciel (middleware) d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configure le serveur pour l’écoute `http://localhost:{port}` . Des vérifications supplémentaires sont effectuées, et les requêtes qui ne proviennent pas du module sont rejetées. Le module ne prend pas en charge le transfert HTTPS : les requêtes sont donc transférées via HTTP, même si IIS les reçoit via HTTPS.

Dès que Kestrel sélectionne la requête dans le module, celle-ci est envoyée (push) dans le pipeline de middlewares d’ASP.NET Core. Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application. L’intergiciel (middleware) ajouté par l’intégration d’IIS met à jour le schéma, l’adresse IP distante et la base du chemin pour prendre en compte le transfert de la requête à Kestrel. La réponse de l’application est ensuite repassée à IIS, qui la renvoie au client HTTP à l’origine de la requête.

`CreateDefaultBuilder` configure le serveur [Kestrel](xref:fundamentals/servers/kestrel) comme serveur web et active l’intégration d’IIS en configurant le chemin et le port de base pour le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module).

Le module ASP.NET Core génère un port dynamique à assigner au processus backend. `CreateDefaultBuilder` appelle la méthode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>. `UseIISIntegration` configure Kestrel pour écouter sur le port dynamique à l’adresse IP localhost (`127.0.0.1`). Si le port dynamique est 1234, Kestrel écoute sur `127.0.0.1:1234`. Cette configuration remplace les autres configurations URL fournies par :

* `UseUrls`
* [API d’écoute Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Configuration](xref:fundamentals/configuration/index) (ou [options --url de ligne de commande](xref:fundamentals/host/web-host#override-configuration))

L’utilisation du module évite les appels à `UseUrls` ou à l’API `Listen` de Kestrel. Si `UseUrls` ou `Listen` est appelé, Kestrel écoute sur le port spécifié uniquement lors de l’exécution de l’application sans IIS.

Pour obtenir de l’aide sur la configuration du module ASP.NET Core, consultez <xref:host-and-deploy/aspnet-core-module>.

Pour plus d’informations sur l’hébergement, consultez [Héberger dans ASP.NET Core](xref:fundamentals/index#host).

## <a name="application-configuration"></a>Configuration de l’application

### <a name="enable-the-iisintegration-components"></a>Activer les composants IISIntegration

Lors de la création d’un ordinateur hôte dans `CreateWebHostBuilder` ( *Program.cs* ), appelez <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> pour activer l’intégration d’IIS :

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

Pour plus d'informations sur le `CreateDefaultBuilder`, consultez <xref:fundamentals/host/web-host#set-up-a-host>.

### <a name="iis-options"></a>Options IIS

| Option                         | Default | Paramètre |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Si `true`, IIS Server définit le `HttpContext.User` authentifié par [Authentification Windows](xref:security/authentication/windowsauth). Si `false`, le serveur fournit uniquement une identité pour `HttpContext.User` et répond aux questions explicitement posées par `AuthenticationScheme`. L’authentification Windows doit être activée dans IIS pour que `AutomaticAuthentication` fonctionne. Pour plus d’informations, consultez [authentification Windows](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Définit le nom d’affichage montré aux utilisateurs sur les pages de connexion. |

Pour configurer les options IIS, incluez une configuration de service pour <xref:Microsoft.AspNetCore.Builder.IISOptions> dans <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>. L’exemple suivant empêche l’application d’être renseignée `HttpContext.Connection.ClientCertificate` :

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

Le [middleware d’intégration IIS](#enable-the-iisintegration-components), qui configure le middleware des en-têtes transférés, et le module ASP.NET Core sont configurés pour transférer le schéma (HTTP/HTTPS) et l’adresse IP distante d’où provient la requête. Une configuration supplémentaire peut être nécessaire pour les applications hébergées derrière des serveurs proxy et des équilibreurs de charge supplémentaires. Pour plus d’informations, consultez l’article [Configurer ASP.NET Core pour l’utilisation de serveurs proxy et d’équilibreurs de charge](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>fichier web.config

Le fichier *web.config* configure le [Module ASP.NET Core](xref:host-and-deploy/aspnet-core-module). La création, la transformation et la publication du fichier *web.config* sont gérées par une cible MSBuild (`_TransformWebConfig`) quand le projet est publié. Cette cible est présente dans les cibles du SDK web (`Microsoft.NET.Sdk.Web`). Le SDK est défini en haut du fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si le projet ne contient pas de fichier *web.config* , le fichier est créé avec le *processPath* et les *arguments* corrects pour configurer le Module ASP.NET Core, puis il est déplacé vers la [sortie publiée](xref:host-and-deploy/directory-structure).

Si un fichier *web.config* se trouve dans le projet, il est transformé avec le *processPath* et les *arguments* corrects pour configurer le Module ASP.NET Core, puis il est déplacé vers la sortie publiée. La transformation ne modifie pas les paramètres de configuration IIS dans le fichier.

Le fichier *web.config* peut fournir des paramètres de configuration IIS supplémentaires qui contrôlent les modules IIS actifs. Pour plus d’informations sur les modules IIS capables de traiter les requêtes avec des applications ASP.NET Core, consultez la rubrique [Modules IIS](xref:host-and-deploy/iis/modules).

Pour empêcher le kit de développement logiciel (SDK) Web de transformer le fichier *web.config* , utilisez la **\<IsTransformWebConfigDisabled>** propriété dans le fichier projet :

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Lorsque vous désactivez le Kit de développement logiciel (SDK) Web en transformant le fichier, le *processPath* et les *arguments* doivent être définis manuellement par le développeur. Pour plus d'informations, consultez <xref:host-and-deploy/aspnet-core-module>.

### <a name="webconfig-file-location"></a>emplacement du fichier web.config

Pour configurer correctement le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) , le fichier *web.config* doit être présent au chemin d’accès [racine du contenu](xref:fundamentals/index#content-root) (en général, le chemin d’accès de base de l’application) de l’application déployée. Il s’agit du même emplacement que le chemin physique du site Web fourni à IIS. Le fichier *web.config* est nécessaire à la racine de l’application pour permettre la publication de plusieurs applications à l’aide de Web Deploy.

Des fichiers sensibles existent sur le chemin d’accès physique de l’application, par exemple *\<assembly>.runtimeconfig.jssur* , *\<assembly> . xml* (commentaires de documentation XML) et *\<assembly>.deps.jssur* . Lorsque le fichier *web.config* est présent et que le site démarre normalement, IIS ne traite pas ces fichiers sensibles s’ils sont demandés. Si le fichier *web.config* est absent, nommé de manière incorrecte ou s’il est incapable de configurer le site pour un démarrage normal, IIS peut traiter des fichiers sensibles publiquement.

**Le fichier *web.config* doit être présent dans le déploiement à tout moment, correctement nommé et capable de configurer le site pour un démarrage normal. Ne supprimez jamais le fichier *web.config* d’un déploiement de production.**

### <a name="transform-webconfig"></a>Transformer web.config

Si vous devez transformer *web.config* lors de la publication (par exemple, définir les variables d’environnement basées sur la configuration, le profil ou l’environnement), consultez <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>Configuration IIS

**Systèmes d’exploitation Windows Server**

Activez le rôle serveur **Serveur Web (IIS)** et établissez des services de rôle.

1. Utilisez l’Assistant **Ajouter des rôles et des fonctionnalités** par le biais du menu **Gérer** ou du lien dans **Gestionnaire de serveur** . À l’étape **Rôles de serveurs** , cochez la case **Serveur Web (IIS)** .

   ![Le rôle Serveur Web IIS est sélectionné à l’étape Sélectionner des rôles de serveurs.](index/_static/server-roles-ws2016.png)

1. Après l’étape **Fonctionnalités** , l’étape **Services de rôle** se charge pour le serveur Web (IIS). Sélectionnez les services de rôle IIS souhaités ou acceptez les services de rôle par défaut fournis.

   ![Les services de rôle par défaut sont sélectionnés à l’étape Sélectionner des services de rôle.](index/_static/role-services-ws2016.png)

   **Authentification Windows (facultatif)**  
   Pour activer l’authentification Windows, développez les nœuds suivants : sécurité du **serveur Web**  >  **Security** . Sélectionnez la fonctionnalité **Authentification Windows** . Pour plus d’informations, consultez [authentification \<windowsAuthentication> Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [configurer l’authentification Windows](xref:security/authentication/windowsauth).

   **WebSockets (facultatif)**  
   WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure. Pour activer les WebSockets, développez les nœuds suivants : développement d’applications de **serveur Web**  >  **Application Development** . Sélectionnez la fonctionnalité **Protocole WebSocket** . Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).

1. Validez l’étape de **Confirmation** pour installer les services et le rôle de serveur web. Un redémarrage du serveur/d’IIS n’est pas nécessaire après l’installation du rôle **Serveur Web (IIS)** .

**Systèmes d’exploitation Windows Desktop**

Activez la **Console de gestion IIS** et les **Services World Wide Web** .

1. Naviguez jusqu’à **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows** (à gauche de l’écran).

1. Ouvrez le nœud **Internet Information Services** . Ouvrez le nœud **Outils de gestion Web** .

1. Cochez la case **Console de gestion IIS** .

1. Cochez la case **Services World Wide Web** .

1. Acceptez les fonctionnalités par défaut pour **Services World Wide Web** ou personnalisez les fonctionnalités d’IIS.

   **Authentification Windows (facultatif)**  
   Pour activer l’authentification Windows, développez les nœuds suivants : **World Wide webing services**  >  **Security** . Sélectionnez la fonctionnalité **Authentification Windows** . Pour plus d’informations, consultez [authentification \<windowsAuthentication> Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [configurer l’authentification Windows](xref:security/authentication/windowsauth).

   **WebSockets (facultatif)**  
   WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure. Pour activer WebSockets, développez les nœuds suivants : fonctionnalités de développement d’applications **World Wide Web services**  >  **Application Development Features** . Sélectionnez la fonctionnalité **Protocole WebSocket** . Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).

1. Si l’installation d’IIS requiert un redémarrage, redémarrez le système.

![Console de gestion IIS et Services World Wide Web sont sélectionnés dans Fonctionnalités de Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Installer le bundle d’hébergement .NET Core

Installez le *bundle d’hébergement .NET Core* sur le système hôte. L’offre groupée installe le Runtime .NET Core, la bibliothèque .NET Core et le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module). Le module permet aux applications ASP.NET Core de s’exécuter derrière IIS.

> [!IMPORTANT]
> Si le bundle d’hébergement est installé avant IIS, l’installation du bundle doit être réparée. Après avoir installé IIS, réexécutez le programme d’installation du bundle d’hébergement.
>
> Si le bundle d’hébergement est installé après l’installation de la version 64 bits (x 64) de .NET Core, les SDK peuvent apparaître manquants ([Aucun SDK .NET Core n’a été détecté](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Pour résoudre le problème, consultez <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="download"></a>Télécharger

1. Accédez à la page [Télécharger .net Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Sélectionnez la version .NET Core de votre choix.
1. Dans la colonne **Run apps - Runtime** , recherchez la ligne de la version du runtime .NET Core souhaitée.
1. Téléchargez le programme d’installation à l’aide du lien d' **hébergement** .

> [!WARNING]
> Certains programmes d’installation contiennent des versions qui sont arrivées à leur fin de vie (EOL) et qui ne sont plus prises en charge par Microsoft. Pour plus d’informations, consultez la [politique de support](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Installer le bundle d’hébergement

1. Exécutez le programme d’installation sur le serveur. Les paramètres suivants sont disponibles lorsque vous exécutez le programme d’installation à partir d’un shell de commande administrateur :

   * `OPT_NO_ANCM=1`: Ignorez l’installation du module ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Ignorez l’installation du Runtime .NET Core. Utilisé lorsque le serveur héberge uniquement [des déploiements autonomes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Ignorez l’installation du Framework partagé ASP.NET (runtime ASP.NET). Utilisé lorsque le serveur héberge uniquement [des déploiements autonomes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Ignorez l’installation des runtimes x86. Utilisez ce paramètre lorsque vous savez que vous n’hébergerez pas d’applications 32 bits. Si vous n’excluez pas d’avoir à héberger des applications 32 bits et 64 bits dans le futur, n'utilisez pas ce paramètre et installez les deux runtimes.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Désactive la vérification de l’utilisation d’une configuration partagée IIS lorsque la configuration partagée ( *applicationHost.config* ) se trouve sur le même ordinateur que l’installation d’IIS. *Disponible uniquement pour les programmes d’installation du pack d’hébergement ASP.NET Core 2.2 ou version ultérieure.* Pour plus d'informations, consultez <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Redémarrez le système ou exécutez les commandes suivantes dans une interface de commande :

   ```console
   net stop was /y
   net start w3svc
   ```
   Le redémarrage d’IIS récupère une modification apportée au CHEMIN D’ACCÈS du système, qui est une variable d’environnement, par le programme d’installation.

Il n’est pas nécessaire d’arrêter manuellement des sites individuels dans IIS lors de l’installation du bundle d’hébergement. Les applications hébergées (sites IIS) redémarrent lors du redémarrage d’IIS. Les applications redémarrent lorsqu’elles reçoivent leur première requête, y compris à partir du [module d’initialisation](#application-initialization-module-and-idle-timeout)de l’application.

ASP.NET Core adopte le comportement de restauration par progression pour les mises à jour correctives des packages d’infrastructure partagés. Lorsque les applications hébergées par IIS redémarrent avec IIS, elles sont chargées avec les dernières versions de correctif de leurs packages référencés lorsqu’elles reçoivent leur première demande. Si IIS n’est pas redémarré, les applications redémarrent et présentent le comportement de restauration par progression lorsque leurs processus de travail sont recyclés et qu’ils reçoivent leur première demande.

> [!NOTE]
> Pour plus d’informations sur la configuration partagée IIS, consultez [Module ASP.NET Core avec configuration partagée des services Internet (IIS)](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Installer Web Deploy lors de la publication avec Visual Studio

Quand vous déployez des applications sur un serveur avec [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), installez la dernière version de Web Deploy sur le serveur. Pour installer Web Deploy, utilisez [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) ou obtenez un programme d’installation directement à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=43717). La méthode recommandée est d’utiliser WebPI. WebPI offre une installation autonome et une configuration pour les fournisseurs d’hébergement.

## <a name="create-the-iis-site"></a>Créer le site IIS

1. Sur le système d’hébergement, créez un dossier pour contenir les fichiers et dossiers publiés de l’application. À l’étape suivante, le chemin du dossier est fourni à IIS en tant que chemin d’accès physique à l’application. Pour plus d’informations sur le dossier de déploiement et la disposition d’un fichier d’une application, consultez <xref:host-and-deploy/directory-structure>.

1. Dans le gestionnaire des services Internet, ouvrez le nœud du serveur dans le panneau **connexions** . Cliquez avec le bouton de droite sur le dossier **Sites** . Sélectionnez **Ajouter un site Web** dans le menu contextuel.

1. Spécifiez le **Nom du site** et définissez le **Chemin physique** sur le dossier de déploiement de l’application. Fournissez la configuration de **liaison** et créez le site Web en sélectionnant **OK** :

   ![Spécifiez le nom du site, le chemin physique et le nom d’hôte à l’étape Ajouter un site Web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Les liaisons génériques de niveau supérieur (`http://*:80/` et `http://+:80`) ne doivent **pas** être utilisées. Les liaisons génériques de niveau supérieur peuvent exposer votre application à des failles de sécurité. Cela s’applique aux caractères génériques forts et faibles. Utilisez des noms d’hôte explicites plutôt que des caractères génériques. Une liaison générique de sous-domaine (par exemple, `*.mysub.com`) ne présente pas ce risque de sécurité si vous contrôlez le domaine parent en entier (par opposition à `*.com`, qui est vulnérable). Consultez la [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) pour plus d’informations.

1. Sous le nœud du serveur, sélectionnez **Pools d’applications** .

1. Cliquez avec le bouton de droite sur le pool d’applications du site et sélectionnez **Paramètres de base** dans le menu contextuel.

1. Dans la fenêtre **Modifier le pool d’applications** , définissez la **version .NET CLR** sur **Aucun code managé**  :

   ![Définissez Aucun code managé pour la version CLR .NET.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core s’exécute dans un processus séparé et gère l’exécution. ASP.NET Core ne repose pas sur le chargement de Desktop CLR (CLR .NET)&mdash;la prise en charge du Common Language Runtime Core (CoreCLR) pour .NET Core est démarrée pour héberger l’application dans le processus Worker. La configuration de la **version CLR .NET** sur **Aucun code managé** est facultative, mais recommandée.

1. *ASP.NET Core 2.2 ou version ultérieure*  : pour un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) 64 bits (x64) qui utilise le [modèle d’hébergement In-process](#in-process-hosting-model), désactivez le pool d’applications pour les processus 32 bits (x86).

   Dans la barre latérale **Actions** du gestionnaire IIS > **Pools d’applications** , sélectionnez **Définir les valeurs par défaut du pool d’applications** ou **Paramètres avancés** . Recherchez **Activer les applications 32 bits** et définissez la valeur sur `False`. Ce paramètre n’affecte pas les applications déployées pour l’[hébergement Out-of-process](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).

1. Vérifiez que l’identité de modèle de processus dispose des autorisations appropriées.

   Si l’identité par défaut du pool d’applications ( **modèle de processus**  >  **Identity** ) est **Identity** remplacée par une autre identité, vérifiez que la nouvelle identité dispose des autorisations nécessaires pour accéder au dossier de l’application, à la base de données et à d’autres ressources requises. Par exemple, le pool d’applications nécessite l’accès en lecture et en écriture aux dossiers dans lesquels l’application lit et écrit des fichiers.

**Configuration de l’authentification Windows (facultatif)**  
Pour plus d'informations, consultez la rubrique [Configurer l’authentification Windows](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Déployer l’application

Déployez l’application dans le dossier **Chemin d’accès physique** IIS qui a été créé dans la section [Créer le site IIS](#create-the-iis-site). [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) est le mécanisme recommandé pour le déploiement, mais il existe plusieurs options pour déplacer l’application à partir du dossier *publier* du projet vers le dossier de déploiement du système d’hébergement.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy avec Visual Studio

Pour découvrir comment créer un profil de publication pour une utilisation avec Web Deploy, consultez la rubrique [Profils de publication Visual Studio pour le déploiement d’applications ASP.NET Core](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles). Si le fournisseur d’hébergement fournit un profil de publication ou prend en charge sa création, téléchargez son profil et importez-le à l’aide de la boîte de dialogue **Publier** de Visual Studio :

![Boîte de dialogue Publier](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy en dehors de Visual Studio

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) peut également être utilisé en dehors de Visual Studio à partir de la ligne de commande. Pour plus d’informations, consultez [Web Deployment Tool (Outil de déploiement Web)](/iis/publish/using-web-deploy/use-the-web-deployment-tool).

### <a name="alternatives-to-web-deploy"></a>Alternatives à Web Deploy

Utilisez la méthode de votre choix, telle que la copie manuelle, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy) ou [PowerShell](/powershell/), pour déplacer l’application vers le système d’hébergement.

Pour plus d’informations sur le déploiement d’ASP.NET Core sur IIS, consultez la section [Déploiement de ressources pour les administrateurs IIS](#deployment-resources-for-iis-administrators).

## <a name="browse-the-website"></a>Parcourir le site Web

Une fois que l’application est déployée sur le système hôte, envoyez une requête à l’un des points de terminaison publics de l’application.

Dans l’exemple suivant, le site est lié à un **nom d’hôte** IIS de `www.mysite.com` sur le **Port** `80`. Une demande est faite à `http://www.mysite.com` :

![Le navigateur Microsoft Edge a chargé la page de démarrage IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Fichiers de déploiement verrouillés

Les fichiers dans le dossier de déploiement sont verrouillés quand l’application est en cours d’exécution. Les fichiers verrouillés ne peuvent pas être remplacés au cours du déploiement. Pour libérer des fichiers verrouillés dans un déploiement, arrêtez le pool d’applications à l’aide **d’une** des approches suivantes :

* Utilisez Web Deploy et référencez `Microsoft.NET.Sdk.Web` dans le fichier projet. Un fichier *app_offline.htm* est placé à la racine du répertoire de l’application web. Quand le fichier est présent, le module ASP.NET Core ferme l’application normalement et sert le fichier *app_offline.htm* pendant le déploiement. Pour plus d’informations, consultez les [Informations de référence sur la configuration du module ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Arrêtez manuellement le pool d’applications dans le Gestionnaire IIS sur le serveur.
* Utilisez PowerShell pour supprimer des *app_offline.htm* (nécessite PowerShell 5 ou version ultérieure) :

  ```powershell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Protection de données

La [pile de protection des données ASP.NET Core](xref:security/data-protection/introduction) est utilisée par plusieurs [intergiciels (middlewares)](xref:fundamentals/middleware/index) ASP.NET Core, y compris l’intergiciel utilisé dans l’authentification. Même si les API de protection des données ne sont pas appelées par le code de l’utilisateur, la protection des données doit être configurée avec un script de déploiement ou dans un code utilisateur pour créer un [magasin de clés](xref:security/data-protection/implementation/key-management) de chiffrement persistantes. Si la protection des données n’est pas configurée, les clés sont conservées en mémoire et ignorées au redémarrage de l’application.

Si le Key Ring est stocké en mémoire, au redémarrage de l’application :

* Tous les cookie jetons d’authentification de base sont invalidés. 
* Les utilisateurs doivent se reconnecter pour envoyer leur prochaine demande. 
* toutes les données protégées par le Key Ring ne peuvent plus être déchiffrées. Cela peut inclure des [jetons CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) et des ASP.NET Core les de la [MVC cookie ](xref:fundamentals/app-state#tempdata).

Pour configurer la protection des données sous IIS afin de rendre persistante le Key Ring, adoptez **l’une** des approches suivantes :

* **Créer des clés de Registre de la protection des données**

  Les clés de la protection des données utilisées par les applications ASP.NET Core sont stockées dans le registre externe aux applications. Afin de conserver les clés pour une application donnée, créez des clés de Registre pour le pool d’applications.

  Pour les installations IIS autonomes hors d’une batterie de serveurs, le [script PowerShell Provision-AutoGenKeys.ps1 de protection des données](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) peut être utilisé pour chaque pool d’applications utilisé avec une application ASP.NET Core. Ce script crée une clé de Registre dans le Registre HKLM, accessible uniquement pour le compte du processus Worker du pool d’applications de l’application. Les clés sont chiffrées au repos à l’aide de DPAPI avec une clé à l’échelle de la machine.

  Dans les scénarios de batterie de serveurs Web, vous pouvez configurer une application afin qu’elle utilise un chemin UNC pour stocker son Key Ring de protection des données. Par défaut, les clés de protection des données ne sont pas chiffrées. Vérifiez que les autorisations de fichiers pour le partage réseau sont limitées au compte Windows sous lequel s’exécute l’application. Un certificat X509 peut être utilisé pour protéger les clés au repos. Mettez en œuvre un mécanisme permettant aux utilisateurs de charger des certificats : placez les certificats dans le magasin de certificats approuvés de l’utilisateur et vérifiez qu’ils sont disponibles sur toutes les machines où s’exécute l’application de l’utilisateur. Pour plus d’informations, consultez [Configurer la protection des données ASP.NET Core](xref:security/data-protection/configuration/overview).

* **Configurer le pool d’applications IIS pour charger le profil utilisateur**

  Ce paramètre se trouve dans la section **Modèle de processus** sous les **Paramètres avancés** du pool d’applications. Affectez la valeur `True` à **Charger le profil utilisateur** . Lorsqu’elle est définie sur `True`, les clés sont stockées dans le répertoire du profil utilisateur, protégées à l’aide de DPAPI avec une clé propre au compte d’utilisateur utilisé pour le pool d’applications. Les clés sont persistantes dans le dossier *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* .

  L’[attribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) du pool d’applications doit également être activé. La valeur par défaut de `setProfileEnvironment` est `true`. Dans certains scénarios (par exemple pour le système d’exploitation Windows), `setProfileEnvironment` est défini sur `false`. Si les clés ne sont pas stockées dans le répertoire de profil utilisateur comme prévu :

  1. Accédez au dossier *%windir%/system32/inetsrv/config* .
  1. Ouvrez le fichier *applicationHost.config* .
  1. Recherchez l’élément `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Confirmez que l’attribut `setProfileEnvironment` n’est pas présent, ce qui implique que la valeur par défaut est `true`, ou définissez de manière explicite la valeur de l’attribut sur `true`.

* **Utiliser le système de fichiers comme magasin de Key Ring**

  Ajustez le code d’application pour [utiliser le système de fichiers comme magasin de porte-clés](xref:security/data-protection/configuration/overview). Utilisez un certificat X509 pour protéger le Key Ring et vérifiez qu’il s’agit d’un certificat approuvé. S’il s’agit d’un certificat auto-signé, placez-le dans le magasin Racine approuvée.

  Quand vous utilisez IIS dans une batterie de serveurs web :

  * Utilisez un partage de fichiers accessible par tous les ordinateurs.
  * Déployez un certificat X509 sur chaque ordinateur. Configurez la [protection des données dans le code](xref:security/data-protection/configuration/overview).

* **Définir une stratégie au niveau de l’ordinateur pour la protection des données**

  Le système de protection des données offre une prise en charge limitée de la définition d’une [stratégie au niveau de l’ordinateur](xref:security/data-protection/configuration/machine-wide-policy) par défaut pour toutes les applications qui utilisent les API de protection des données. Pour plus d'informations, consultez <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Répertoires virtuels

Les [répertoires virtuels IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) ne sont pas pris en charge avec les applications ASP.NET Core. Une application peut être hébergée en tant que [sous-application](#sub-applications).

## <a name="sub-applications"></a>Sous-applications

Une application ASP.NET Core peut être hébergée en tant que [sous-application IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications). Le chemin d'accès de la sous-application devient partie intégrante de l’URL de l’application racine.

Une sous-application ne doit pas inclure le module ASP.NET Core en tant que gestionnaire. Si le module est ajouté en guise de gestionnaire dans le fichier *web.config* d’une sous-application, une *Erreur de serveur interne 500.19* faisant référence au fichier config défaillant est reçue lors de la tentative de navigation dans la sous-application.

L’exemple suivant présente un fichier *web.config* publié pour une sous-application ASP.NET Core :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Si vous hébergez une sous-application non-ASP.NET Core sous une application ASP.NET Core, supprimez explicitement le gestionnaire hérité dans le fichier *web.config* de la sous-application :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Les liens de ressources statiques au sein de la sous-application doivent utiliser la notation tilde-barre oblique (`~/`). La notation tilde-barre oblique déclenche un [Tag Helper](xref:mvc/views/tag-helpers/intro) afin d’ajouter l’élément pathbase de la sous-application au lien relatif rendu. Pour une sous-application dans `/subapp_path`, une image liée à `src="~/image.png"` est restituée sous la forme `src="/subapp_path/image.png"`. Le composant Static File Middleware de l’application racine ne traite la demande de fichiers statiques. La demande est traitée par le composant Static File Middleware de la sous-application.

Si l’attribut `src` d’une ressource statique est défini sur un chemin d’accès absolu (par exemple, `src="/image.png"`), le lien apparaît sans l’élément pathbase de la sous-application. Le composant Static File Middleware de l’application racine tente de traiter la ressource à partir de l’élément [webroot](xref:fundamentals/index#web-root) de l’application racine, ce qui entraîne une erreur *404 - Introuvable* , sauf si la ressource statique est disponible depuis l’application racine.

Pour héberger une application ASP.NET Core en tant que sous-application d’une autre application ASP.NET Core :

1. Établissez un pool d’applications pour la sous-application. Définissez la **version CLR .NET** sur **Aucun code managé** car la prise en charge du Common Language Runtime Core (CoreCLR) pour Core .Net est démarrée pour héberger l’application dans le processus Worker et non dans le Desktop CLR (CLR .Net).

1. Ajoutez le site racine dans IIS Manager en plaçant la sous-application dans un dossier du site racine.

1. Cliquez avec le bouton droit sur le dossier de la sous-application dans IIS Manager, puis sélectionnez **Convertir en application** .

1. Dans la boîte de dialogue **Ajouter une application** , utilisez le bouton **Sélectionner** de l’option **Pool d’applications** pour affecter le pool d’applications que vous avez créé pour la sous-application. Sélectionnez **OK** .

L’attribution d’un pool d’applications distinct à la sous-application est obligatoire lorsque vous utilisez le modèle d’hébergement in-process.

Pour plus d’informations sur le modèle d’hébergement in-process et sur la configuration du module ASP.NET Core, consultez <xref:host-and-deploy/aspnet-core-module> .

## <a name="configuration-of-iis-with-webconfig"></a>Configuration d’IIS avec web.config

La configuration d’IIS est influencée par la section `<system.webServer>` de *web.config* pour les scénarios IIS qui sont fonctionnels pour les applications ASP.NET Core avec le module ASP.NET Core. Par exemple, la configuration IIS est fonctionnelle pour la compression dynamique. Si IIS est configuré au niveau du serveur pour utiliser la compression dynamique, l’élément `<urlCompression>` dans le fichier *web.config* de l’application peut le désactiver pour une application ASP.NET Core.

Pour plus d'informations, consultez les rubriques suivantes :

* [Référence de configuration pour \<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Pour définir des variables d’environnement pour des applications individuelles s’exécutant dans des pools d’applications isolés (pris en charge pour IIS 10,0 ou version ultérieure), consultez la section *commandeAppCmd.exe* de la rubrique [variables \<environmentVariables> d’environnement](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) dans la documentation de référence IIS.

## <a name="configuration-sections-of-webconfig"></a>Sections de configuration de web.config

Les sections de configuration des applications ASP.NET 4.x dans *web.config* ne sont pas utilisées par les applications ASP.NET Core pour la configuration :

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

Les applications ASP.NET Core sont configurées à l’aide d’autres fournisseurs de configuration. Pour plus d’informations, consultez [configuration](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Pools d'applications

Quand vous hébergez plusieurs sites Web sur un même serveur, nous vous recommandons d’isoler les applications les unes des autres en exécutant chaque application dans son propre pool d’applications. La boîte de dialogue **Ajouter un site Web** d’IIS applique cette configuration par défaut. Quand un **Nom du site** est fourni, le texte est automatiquement transféré vers la zone de texte **Pool d’applications** . Un nouveau pool d’applications est créé avec le nom du site une fois qu’il est ajouté.

## <a name="application-pool-no-locidentity"></a>Pool d’applications Identity

Un compte d’identité du pool d’applications permet à une application de s’exécuter sous un compte unique sans qu’il soit nécessaire de créer et de gérer des domaines ou des comptes locaux. Sur IIS 8.0 ou version ultérieure, le processus Worker d’administration IIS (WAS) crée un compte virtuel avec le nom du nouveau pool d’applications et exécute les processus Worker du pool d’applications sous ce compte par défaut. Dans la console de gestion IIS, sous **Paramètres avancés** pour le pool d’applications, assurez-vous que le **Identity** est configuré pour utiliser **ApplicationPool Identity** :

![Boîte de dialogue Paramètres avancés du pool applications](index/_static/apppool-identity.png)

Le processus de gestion IIS crée un identificateur sécurisé avec le nom du pool d’applications dans le système de sécurité Windows. Les ressources peuvent être sécurisées à l’aide de cette identité. Toutefois, cette identité n’est pas un compte d’utilisateur réel et n’apparaît pas dans la console de gestion d’utilisateurs Windows.

Si le processus Worker IIS a besoin d’un accès élevé à l’application, modifiez la liste de contrôle d’accès (ACL) du répertoire contenant l’application :

1. Ouvrez l’Explorateur Windows et accédez au répertoire.

1. Cliquez avec le bouton droit sur le répertoire et sélectionnez **Propriétés** .

1. Sous l’onglet **Sécurité** , sélectionnez le bouton **Modifier** , puis le bouton **Ajouter** .

1. Sélectionnez le bouton **Emplacements** , puis vérifiez que le système est sélectionné.

1. Entrez **IIS AppPool\\<app_pool_name>** dans la zone **Entrer les noms des objets à sélectionner** . Sélectionnez le bouton **Vérifier les noms** . Pour le *DefaultAppPool* , vérifiez les noms à l’aide de **IIS AppPool\DefaultAppPool** . Lorsque le bouton **Vérifier les noms** est sélectionné, une valeur de **DefaultAppPool** est indiquée dans la zone des noms d’objets. Il n’est pas possible d’entrer le nom du pool d’applications directement dans la zone des noms d’objets. Utilisez le format **IIS AppPool\\<app_pool_name>** lors de la vérification du nom de l’objet.

   ![Sélectionnez la boîte de dialogue utilisateurs ou groupes pour le dossier d’applications : ajoutez le nom du pool d’applications « DefaultAppPool » à « IIS AppPool\" dans la zone des noms d’objets avant de sélectionner « Vérifier les noms ».](index/_static/select-users-or-groups-1.png)

1. Sélectionnez **OK** .

   ![Sélectionnez la boîte de dialogue utilisateurs ou groupes pour le dossier d’applications : après avoir sélectionné « Vérifier les noms », le nom d’objet « DefaultAppPool » est indiqué dans la zone des noms d’objets.](index/_static/select-users-or-groups-2.png)

1. Les autorisations Lire &amp; exécuter doivent être accordées par défaut. Fournissez des autorisations supplémentaires si nécessaire.

L’accès peut également être octroyé par le biais d’une invite de commandes à l’aide de l’outil **ICACLS** . À l’aide de *DefaultAppPool* en exemple, la commande suivante est utilisée :

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Pour plus d’informations, consultez la rubrique [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="http2-support"></a>Assistance HTTP/2

[HTTP/2](https://httpwg.org/specs/rfc7540.html) est pris en charge pour les déploiements out-of-process qui répondent aux exigences de base suivantes :

* Windows Server 2016/Windows 10 ou version ultérieure ; IIS 10 ou version ultérieure
* Les connexions au serveur périphérique public utilisent HTTP/2, mais la connexion de proxy inverse pour le [serveur Kestrel](xref:fundamentals/servers/kestrel) utilise HTTP/1.1.
* Version cible de .Net Framework : non applicable aux déploiements out-of-process, étant donné que la connexion HTTP/2 est gérée entièrement par IIS.
* TLS 1.2 ou connexion ultérieure

Si une connexion HTTP/2 est établie, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) retourne `HTTP/1.1`.

HTTP/2 est activé par défaut. Les connexions reviennent à HTTP/1.1 si une connexion HTTP/2 n’est pas établie. Pour plus d’informations sur la configuration de HTTP/2 avec les déploiements IIS, consultez [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Requêtes préliminaires CORS

*Cette section s’applique uniquement aux applications ASP.NET Core qui ciblent le .NET Framework.*

Pour une application ASP.NET Core qui cible le .NET Framework, les requêtes OPTIONS ne sont pas transmises à l’application par défaut dans IIS. Pour savoir comment configurer les gestionnaires IIS de l’application dans *web.config* pour passer des demandes d’options, consultez [activer les demandes Cross-Origin dans API Web ASP.NET 2 : fonctionnement de cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="deployment-resources-for-iis-administrators"></a>Déploiement de ressources pour les administrateurs IIS

* [Documentation IIS](/iis)
* [Bien démarrer avec le Gestionnaire IIS dans IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [Déploiement d’applications .NET Core](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/troubleshoot>
* <xref:index>
* [Site officiel de Microsoft IIS](https://www.iis.net/)
* [Bibliothèque de contenu technique Windows Server](/windows-server/windows-server)
* [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>

::: moniker-end
