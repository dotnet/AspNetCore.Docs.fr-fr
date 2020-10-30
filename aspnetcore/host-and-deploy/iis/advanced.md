---
title: Configuration avancée
author: rick-anderson
description: Configuration avancée avec le module ASP.NET Core et Internet Information Services (IIS).
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058610"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a>Configuration avancée du module ASP.NET Core et des services IIS

Cet article traite des options de configuration avancées et des scénarios pour le module ASP.NET Core et IIS.

## <a name="modify-the-stack-size"></a>Modifier la taille de pile

*S’applique uniquement lors de l’utilisation du modèle d’hébergement in-process.*

Configurez la taille de la pile managée en utilisant le `stackSize` paramètre en octets dans le `web.config` fichier. La taille par défaut est de 1 048 576 octets (1 Mo). L’exemple suivant modifie la taille de la pile à 2 Mo (2 097 152 octets) :

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>La configuration du proxy utilise le protocole HTTP et un jeton d’appariement

*S’applique uniquement à l’hébergement out-of-process.*

Le proxy créé entre le module ASP.NET Core et Kestrel utilise le protocole HTTP. Il n’existe aucun risque d’écoute clandestine du trafic entre le module et Kestrel à partir d’un emplacement sur le serveur.

Un jeton d’appariement est utilisé pour garantir que les demandes reçues par Kestrel ont été traitées par IIS et ne proviennent pas d’une autre source. Le jeton d’appariement est créé et défini dans une variable d’environnement (`ASPNETCORE_TOKEN`) par le module. Le jeton d’appariement est également défini dans un en-tête (`MS-ASPNETCORE-TOKEN`) sur toutes les demandes traitées. L'intergiciel IIS vérifie chaque demande qu’il reçoit pour confirmer que la valeur d’en-tête du jeton d'appariement correspond à la valeur de la variable d’environnement. Si les valeurs de jeton ne correspondent pas, la demande est connectée et rejetée. La variable d’environnement du jeton d'appariement et le trafic entre le module et Kestrel ne sont pas accessibles à partir d’un emplacement sur le serveur. Sans connaître la valeur du jeton d'appariement, une personne malveillante ne peut pas soumettre des demandes qui contournent la vérification de l’intergiciel IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Module ASP.NET Core avec une configuration partagée IIS

Le programme d’installation du module ASP.NET Core s’exécute avec les privilèges du `TrustedInstaller` compte. Étant donné que le compte système local ne dispose pas de l’autorisation de modification pour le chemin d’accès de partage utilisé par la configuration partagée IIS, le programme d’installation lève une erreur d’accès refusé lors de la tentative de configuration des paramètres du module dans le `applicationHost.config` fichier sur le partage.

Quand une configuration partagée IIS est utilisée sur la même machine que l’installation IIS, il convient d’exécuter le programme d’installation du bundle d’hébergement ASP.NET Core avec le paramètre `OPT_NO_SHARED_CONFIG_CHECK` défini sur `1` :

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Quand le chemin de la configuration partagée ne se trouve pas sur la même machine que l’installation IIS, effectuez ces étapes :

1. Désactivez la configuration partagée IIS.
1. Exécutez le programme d’installation.
1. Exportez le fichier mis à jour `applicationHost.config` vers le partage de fichiers.
1. Réactivez la configuration partagée IIS.

## <a name="data-protection"></a>Protection de données

La [pile de protection des données ASP.NET Core](xref:security/data-protection/introduction) est utilisée par plusieurs [intergiciels (middlewares)](xref:fundamentals/middleware/index) ASP.NET Core, y compris l’intergiciel utilisé dans l’authentification. Même si les API de protection des données ne sont pas appelées par le code de l’utilisateur, la protection des données doit être configurée avec un script de déploiement ou dans un code utilisateur pour créer un [magasin de clés](xref:security/data-protection/implementation/key-management) de chiffrement persistantes. Si la protection des données n’est pas configurée, les clés sont conservées en mémoire et ignorées au redémarrage de l’application.

Si l’anneau de clé de protection des données est stocké en mémoire au redémarrage de l’application :

* Tous les cookie jetons d’authentification de base sont invalidés. 
* Les utilisateurs doivent se reconnecter pour envoyer leur prochaine demande. 
* toutes les données protégées par le Key Ring ne peuvent plus être déchiffrées. Cela peut inclure des [jetons CSRF](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) et des ASP.NET Core les de la [MVC cookie ](xref:fundamentals/app-state#tempdata).

Pour configurer la protection des données sous IIS afin de rendre persistante le Key Ring, adoptez **l’une** des approches suivantes :

* **Créer des clés de registre de protection des données**

  Les clés de protection des données utilisées par les applications ASP.NET Core sont stockées dans le registre à l’extérieur des applications. Pour conserver les clés pour une application donnée, créez des clés de Registre pour le pool d’applications.

  Pour les installations IIS autonomes hors d’une batterie de serveurs, le [script PowerShell Provision-AutoGenKeys.ps1 de protection des données](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) peut être utilisé pour chaque pool d’applications utilisé avec une application ASP.NET Core. Ce script crée une clé de Registre dans le Registre HKLM qui est accessible uniquement au compte du processus de travail du pool d’applications de l’application. Les clés sont chiffrées au repos à l’aide de DPAPI avec une clé à l’échelle de la machine.

  Dans les scénarios de batterie de serveurs Web, une application peut être configurée pour utiliser un chemin UNC pour stocker son anneau de clé de protection des données. Par défaut, les clés ne sont pas chiffrées. Assurez-vous que les autorisations de fichiers pour le partage réseau sont limitées au compte Windows sous lequel l’application s’exécute. Un certificat X509 peut être utilisé pour protéger les clés au repos. Prenons l’exemple d’un mécanisme permettant aux utilisateurs de télécharger des certificats. Placez les certificats dans le magasin de certificats approuvés de l’utilisateur et vérifiez qu’ils sont disponibles sur toutes les machines où s’exécute l’application de l’utilisateur. Pour plus d'informations, consultez <xref:security/data-protection/configuration/overview>.

* **Configurer le pool d’applications IIS pour charger le profil utilisateur**

  Ce paramètre se trouve dans la section **Modèle de processus** sous les **Paramètres avancés** du pool d’applications. Affectez la valeur `True` à **Charger le profil utilisateur** . Lorsqu’elle est définie sur `True`, les clés sont stockées dans le répertoire du profil utilisateur, protégées à l’aide de DPAPI avec une clé propre au compte d’utilisateur utilisé pour le pool d’applications. Les clés sont conservées dans le `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` dossier.

  L' [ `setProfileEnvironment` attribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) du pool d’applications doit également être activé. La valeur par défaut de `setProfileEnvironment` est `true`. Dans certains scénarios (par exemple pour le système d’exploitation Windows), `setProfileEnvironment` est défini sur `false`. Si les clés ne sont pas stockées dans le répertoire de profil utilisateur comme prévu :

  1. Accédez au dossier `%windir%/system32/inetsrv/config`.
  1. Ouvrez le fichier `applicationHost.config` .
  1. Recherchez l’élément `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.
  1. Confirmez que l’attribut `setProfileEnvironment` n’est pas présent, ce qui implique que la valeur par défaut est `true`, ou définissez de manière explicite la valeur de l’attribut sur `true`.

* **Utiliser le système de fichiers comme magasin de Key Ring**

  Ajustez le code d’application pour [utiliser le système de fichiers comme magasin de porte-clés](xref:security/data-protection/configuration/overview). Utilisez un certificat X509 pour protéger le Key Ring et vérifiez qu’il s’agit d’un certificat approuvé. S’il s’agit d’un certificat auto-signé, placez-le dans le magasin Racine approuvée.

  Quand vous utilisez IIS dans une batterie de serveurs web :

  * Utilisez un partage de fichiers accessible par tous les ordinateurs.
  * Déployez un certificat X509 sur chaque ordinateur. Configurez la [protection des données dans le code](xref:security/data-protection/configuration/overview).

* **Définir une stratégie au niveau de l’ordinateur pour la protection des données**

  Le système de protection des données offre une prise en charge limitée de la définition d’une stratégie par défaut au niveau de l' [ordinateur](xref:security/data-protection/configuration/machine-wide-policy) pour toutes les applications qui utilisent les API de protection des données. Pour plus d'informations, consultez <xref:security/data-protection/introduction>.

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

1. Accédez à **panneau de configuration**  >  **programmes** programmes  >  **et fonctionnalités**  >  **activer ou désactiver des fonctionnalités Windows** (côté gauche de l’écran).

1. Ouvrez le nœud **Internet Information Services** . Ouvrez le nœud **Outils de gestion Web** .

1. Cochez la case **Console de gestion IIS** .

1. Cochez la case **Services World Wide Web** .

1. Acceptez les fonctionnalités par défaut pour **Services World Wide Web** ou personnalisez les fonctionnalités d’IIS.

   **Authentification Windows (facultatif)**  
   Pour activer l’authentification Windows, développez les nœuds suivants : **World Wide webing services**  >  **Security** . Sélectionnez la fonctionnalité **Authentification Windows** . Pour plus d’informations, consultez [authentification `<windowsAuthentication>` Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) et [configurer l’authentification Windows](xref:security/authentication/windowsauth).

   **WebSockets (facultatif)**  
   WebSockets est pris en charge avec ASP.NET Core 1.1 ou version ultérieure. Pour activer WebSockets, développez les nœuds suivants : fonctionnalités de développement d’applications **World Wide Web services**  >  **Application Development Features** . Sélectionnez la fonctionnalité **Protocole WebSocket** . Pour plus d’informations, consultez [WebSockets](xref:fundamentals/websockets).

1. Si l’installation d’IIS requiert un redémarrage, redémarrez le système.

![Console de gestion IIS et Services World Wide Web sont sélectionnés dans Fonctionnalités de Windows.](index/_static/windows-features-win10.png)

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

## <a name="application-pools"></a>Pools d'applications

L’isolation des pools d’applications est déterminée par le modèle d’hébergement :

* Hébergement in-process : les applications doivent être exécutées dans des pools d’applications distincts.
* Hébergement hors processus : nous vous recommandons d’isoler les applications les unes des autres en exécutant chaque application dans son propre pool d’applications.

La boîte de dialogue **Ajouter un site web** d’IIS applique un seul pool d’applications par application par défaut. Quand un **Nom du site** est fourni, le texte est automatiquement transféré vers la zone de texte **Pool d’applications** . Un nouveau pool d’applications est créé avec le nom du site une fois qu’il est ajouté.

## <a name="application-pool-no-locidentity"></a>Pool d’applications Identity

Un compte d’identité du pool d’applications permet à une application de s’exécuter sous un compte unique sans qu’il soit nécessaire de créer et de gérer des domaines ou des comptes locaux. Sur IIS 8.0 ou version ultérieure, le processus Worker d’administration IIS (WAS) crée un compte virtuel avec le nom du nouveau pool d’applications et exécute les processus Worker du pool d’applications sous ce compte par défaut. Dans la console de gestion IIS, sous **Paramètres avancés** pour le pool d’applications, assurez-vous que le **Identity** est configuré pour utiliser `ApplicationPoolIdentity` :

![Boîte de dialogue Paramètres avancés du pool applications](index/_static/apppool-identity.png)

Le processus de gestion IIS crée un identificateur sécurisé avec le nom du pool d’applications dans le système de sécurité Windows. Les ressources peuvent être sécurisées à l’aide de cette identité. Toutefois, cette identité n’est pas un compte d’utilisateur réel et n’apparaît pas dans la console de gestion d’utilisateurs Windows.

Si le processus Worker IIS a besoin d’un accès élevé à l’application, modifiez la liste de contrôle d’accès (ACL) du répertoire contenant l’application :

1. Ouvrez l’Explorateur Windows et accédez au répertoire.

1. Cliquez avec le bouton droit sur le répertoire et sélectionnez **Propriétés** .

1. Sous l’onglet **Sécurité** , sélectionnez le bouton **Modifier** , puis le bouton **Ajouter** .

1. Sélectionnez le bouton **Emplacements** , puis vérifiez que le système est sélectionné.

1. Entrez `IIS AppPool\{APP POOL NAME}` format, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications, dans **la zone Entrez les noms des objets à sélectionner** . Sélectionnez le bouton **Vérifier les noms** . Pour l’option *DefaultAppPool* , vérifiez les noms à l’aide de `IIS AppPool\DefaultAppPool` . Lorsque le bouton **vérifier les noms** est sélectionné, la valeur `DefaultAppPool` est indiquée dans la zone noms d’objets. Il n’est pas possible d’entrer le nom du pool d’applications directement dans la zone des noms d’objets. Utilisez le `IIS AppPool\{APP POOL NAME}` format, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications, lors de la vérification du nom de l’objet.

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

Pour un déploiement in-process lors de l’établissement d’une connexion HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) signale `HTTP/2` . Pour un déploiement hors processus lors de l’établissement d’une connexion HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) signale `HTTP/1.1` .

Pour plus d’informations sur les modèles d’hébergement in-process et out-of-process, consultez <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 est activé par défaut. Les connexions reviennent à HTTP/1.1 si une connexion HTTP/2 n’est pas établie. Pour plus d’informations sur la configuration de HTTP/2 avec les déploiements IIS, consultez [HTTP/2 sur IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>Requêtes préliminaires CORS

*Cette section s’applique uniquement aux applications ASP.NET Core qui ciblent le .NET Framework.*

Pour une application ASP.NET Core qui cible le .NET Framework, les requêtes OPTIONS ne sont pas transmises à l’application par défaut dans IIS. Pour savoir comment configurer les gestionnaires IIS de l’application dans `web.config` pour passer des demandes d’options, consultez [activer les demandes Cross-origin dans API Web ASP.NET 2 : fonctionnement de cors](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Module d’initialisation de l’application et délai d’inactivité

Quand ils sont hébergés dans IIS par le Module ASP.NET Core version 2 :

* [Module d’initialisation d’application](#application-initialization-module): le processus [de](xref:host-and-deploy/iis/in-process-hosting) l’application hébergée dans le processus ou [hors processus](xref:host-and-deploy/iis/out-of-process-hosting) peut être configuré pour démarrer automatiquement lors du redémarrage d’un processus de travail ou du redémarrage du serveur.
* [Délai d’inactivité](#idle-timeout): [le processus hébergé par](xref:host-and-deploy/iis/in-process-hosting) l’application peut être configuré pour ne pas expirer pendant les périodes d’inactivité.

### <a name="application-initialization-module"></a>Module d’initialisation de l’application

*S’applique aux applications hébergées in-process et hors processus.*

[L’Initialisation d’application IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) est une fonctionnalité IIS qui envoie une requête HTTP à l’application lorsque le pool d’applications démarre ou est recyclé. La requête déclenche le démarrage de l’application. Par défaut, IIS émet une requête à l’URL racine de l’application (`/`) pour initialiser l’application (consultez les [ressources supplémentaires](#application-initialization-module-and-idle-timeout-additional-resources) pour plus d’informations sur la configuration).

Vérifiez que la fonctionnalité de rôle Initialisation d’application IIS est activée :

Sur Windows 7 ou systèmes de bureau de version ultérieure lorsque vous utilisez IIS localement :

1. Accédez à **panneau de configuration**  >  **programmes** programmes  >  **et fonctionnalités**  >  **activer ou désactiver des fonctionnalités Windows** (côté gauche de l’écran).
1. Ouvrez **Internet Information Services**  >  fonctionnalités de développement d’applications **World Wide Web services**  >  **Application Development Features** .
1. Cochez la case **Initialisation d’application** .

Sur Windows Server 2008 R2 ou version ultérieure :

1. Ouvrez l’ **Assistant Ajout de rôles et de fonctionnalités** .
1. Dans le panneau **Sélectionnez les services de rôle** , ouvrez le nœud **Développement d’applications** .
1. Cochez la case **Initialisation d’application** .

Utilisez une des approches suivantes pour activer le Module d’initialisation de l’application pour le site :

* Utilisation du Gestionnaire IIS :

  1. Sélectionnez **Pools d’applications** dans le volet **Connexions** .
  1. Cliquez avec le bouton de droite sur le pool d’applications de l’application dans la liste, puis sélectionnez **Paramètres avancés** .
  1. Le **mode de démarrage** par défaut est `OnDemand` . Définissez le **mode de démarrage** sur `AlwaysRunning` . Sélectionnez **OK** .
  1. Ouvrez le nœud **Sites** dans le panneau **Connexions** .
  1. Cliquez avec le bouton droit sur l’application, puis sélectionnez **gérer** les  >  **Paramètres avancés** du site Web.
  1. Le paramètre de **préchargement** par défaut activé est `False` . Affectez à l' **option Précharger** la valeur `True` . Sélectionnez **OK** .

* À l’aide de `web.config` , ajoutez l' `<applicationInitialization>` élément avec `doAppInitAfterRestart` `true` la valeur aux `<system.webServer>` éléments du fichier de l’application `web.config` :

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
1. Le **délai d’inactivité par défaut (en minutes)** est de `20` minutes. Définissez le **délai d’inactivité (en minutes)** sur `0` (zéro). Sélectionnez **OK** .
1. Recyclez le processus Worker.

Pour empêcher les applications hébergées [hors processus](xref:host-and-deploy/iis/out-of-process-hosting) d’expirer, utilisez une des approches suivantes :

* Effectuez un test ping de l’application à partir d’un service externe afin de garantir son fonctionnement.
* Si l’application héberge uniquement les services d’arrière-plan, évitez l’hébergement IIS et utilisez un [Service Windows pour héberger l’application ASP.NET Core](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Module d’initialisation de l’application et ressources supplémentaires du délai d’inactivité

* [Initialisation de l’application IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Initialisation de l' `<applicationInitialization>` application ](/iis/configuration/system.webserver/applicationinitialization/).
* [Paramètres du modèle de processus pour un `<processModel>` pool d’applications ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="module-schema-and-configuration-file-locations"></a>Emplacements des fichiers du module, du schéma et de configuration

### <a name="module"></a>Module

**IIS (x86/amd64)** :

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/amd64)** :

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>schéma

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>Configuration

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visual Studio : `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* COMMUN `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

Vous pouvez trouver les fichiers en recherchant `aspnetcore` dans le `applicationHost.config` fichier.
