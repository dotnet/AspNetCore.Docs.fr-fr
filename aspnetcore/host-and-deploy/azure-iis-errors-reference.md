---
title: Informations de référence sur les erreurs courantes pour Azure App Service et IIS avec ASP.NET Core
author: rick-anderson
description: Obtenez des conseils de résolution de problèmes pour les erreurs courantes liées à l’hébergement d’applications ASP.NET Core sur Azure Apps Service et IIS.
monikerRange: '>= aspnetcore-2.1'
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
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: b009cc61a94e618a48d96ecbd770ef6371308f6a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059843"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Informations de référence sur les erreurs courantes pour Azure App Service et IIS avec ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

Cette rubrique décrit les erreurs courantes et fournit des conseils de dépannage pour les erreurs spécifiques lors de l’hébergement d’applications ASP.NET Core sur Azure Apps service et IIS.

Pour obtenir des instructions générales sur la résolution des problèmes, consultez <xref:test/troubleshoot-azure-iis> .

Collectez les informations suivantes :

* Comportement du navigateur (code d’état et message d’erreur)
* Entrées du journal des événements de l’application
  * Azure App Service : consultez <xref:test/troubleshoot-azure-iis> .
  * IIS
    1. Sélectionnez **Démarrer** dans le menu **Windows** , tapez *Observateur d’événements* , puis appuyez sur **Entrée** .
    1. Une fois l’ **Observateur d’événements** ouvert, développez **Journaux Windows** > **Application** dans la barre latérale.
* Entrées de journal stdout et de débogage du module ASP.NET Core
  * Azure App Service : consultez <xref:test/troubleshoot-azure-iis> .
  * IIS : suivez les instructions des sections [création de journal et redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) et [journaux de diagnostic améliorés](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) de la rubrique du module ASP.net core.

Comparez les informations d’erreur aux erreurs courantes suivantes. Si vous trouvez une correspondance, suivez les conseils de dépannage.

La liste d’erreurs de cette rubrique n’est pas exhaustive. Si vous rencontrez une erreur non listée ici, ouvrez un nouveau problème à l’aide du bouton de **Commentaires sur le contenu** situé au bas de cette rubrique, puis fournissez des instructions détaillées sur la manière de reproduire l’erreur.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>La mise à niveau du système d’exploitation a supprimé le Module ASP.NET Core 32 bits

**Journal des applications :** Le fichier DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** du module n’a pas pu se charger. Les données sont erronées.

Résolution des problèmes :

Les fichiers autres que les fichiers de système d’exploitation dans le répertoire **C:\Windows\SysWOW64\inetsrv** ne sont pas conservés pendant la mise à niveau du système d’exploitation. Si le module ASP.NET Core est installé avant la mise à niveau d’un système d’exploitation et si un pool d’applications est exécuté en mode 32 bits après la mise à niveau du système d’exploitation, ce problème se produit. Après une mise à niveau du système d’exploitation, réparez le Module ASP.NET Core. Consultez [installer le bundle d’hébergement .net Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Sélectionnez **Réparer** quand le programme d’installation est exécuté.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Extension de site manquante, extensions de site 32 bits (x86) et 64 bits (x64) installées ou nombre de bits de processus incorrect défini

*S’applique aux applications hébergées par Azure App Services.*

* **Navigateur :** Erreur HTTP 500,0-échec du chargement du gestionnaire de In-Process ANCM

* **Journal des applications :** Échec de l’appel de hostfxr pour rechercher le gestionnaire de demandes InProcess sans rechercher de dépendances natives. Le gestionnaire de requêtes in-process est introuvable. Sortie capturée de l’appel de hostfxr : il n’a pas été possible de trouver une version de Framework compatible. Le framework spécifié « Microsoft.AspNetCore.App », version « {VERSION}-preview-\* » est introuvable. Échec du démarrage de l’application « /LM/W3SVC/1416782824/ROOT ». Code d’erreur : 0x8000ffff.

* **Journal stdout du Module ASP.net Core :** Il n’était pas possible de trouver une version de Framework compatible. Le framework spécifié « Microsoft.AspNetCore.App », version « {VERSION}-preview-\* » est introuvable.

* **Journal de débogage du Module ASP.net Core :** Échec de l’appel de hostfxr pour rechercher le gestionnaire de demandes InProcess sans rechercher de dépendances natives. Cela signifie très probablement que l’application est mal configurée. Vérifiez les versions de Microsoft.NetCore.App et Microsoft.AspNetCore.App ciblées par l’application et installées sur la machine. Échec de HRESULT retourné : 0x8000FFFF. Le gestionnaire de requêtes in-process est introuvable. Il n’a pas été possible de localiser une version compatible du framework. Le framework spécifié « Microsoft.AspNetCore.App », version « {VERSION}-preview-\* » est introuvable.

Résolution des problèmes :

* Si vous exécutez l’application sur un runtime en préversion, installez l’extension de site 32 bits (x86) **ou** 64 bits (x64) qui correspond au nombre de bits de l’application et à la version du runtime de l’application. **N’installez pas les deux extensions ou plusieurs versions du runtime de l’extension.**

  * Runtime ASP.NET Core {RUNTIME VERSION} (x86)
  * Runtime ASP.NET Core {RUNTIME VERSION} (x64)

  Redémarrez l’application. Patientez quelques secondes pour que l’application redémarre.

* Si vous exécutez l’application sur un runtime en préversion et que les deux [extensions de site](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) 32 bits (x86) et 64 bits (x64) sont installées, désinstallez l’extension de site qui ne correspond pas au nombre de bits de l’application. Après avoir supprimé l’extension de site, redémarrez l’application. Patientez quelques secondes pour que l’application redémarre.

* Si vous exécutez l’application sur un runtime en préversion et que le nombre de bits de l’extension de site correspond à celui de l’application, vérifiez que la *version du runtime* de l’extension de site en préversion correspond à la version du runtime de l’application.

* Vérifiez que la **plateforme** de l’application dans **Paramètres de l’application** correspond au nombre de bits de l’application.

Pour plus d'informations, consultez <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Une application x86 est déployée mais le pool d’applications n’est pas activé pour les applications 32 bits

* **Navigateur :** Erreur HTTP 500,30-ANCM In-Process échec du démarrage

* **Journal des applications :** L’application'/LM/W3SVC/5/ROOT’avec la racine physique' {PATH} 'a atteint une exception managée inattendue, code d’exception = ' 0xe0434352 '. Pour plus d’informations, consultez les journaux stderr. L’application « /LM/W3SVC/5/ROOT » ayant pour racine physique « {PATH} » n’a pas pu charger clr et l’application managée. Sortie prématurée du thread de travail CLR

* **Journal stdout du Module ASP.net Core :** Le fichier journal est créé, mais vide.

* **Journal de débogage du Module ASP.net Core :** Échec de HRESULT retourné : 0x8007023e

Ce scénario est intercepté par le kit SDK au moment de la publication d’une application autonome. Le kit SDK génère une erreur si le RID ne correspond pas à la cible de la plateforme (par exemple, un RID `win10-x64` avec `<PlatformTarget>x86</PlatformTarget>` dans le fichier projet).

Résolution des problèmes :

Pour un déploiement dépendant du framework x86 (`<PlatformTarget>x86</PlatformTarget>`), activez le pool d’applications IIS pour les applications 32 bits. Dans le Gestionnaire IIS, ouvrez les **Paramètres avancés** du pool d’applications, puis affectez à l’option **Activer les applications 32 bits** la valeur **Vrai** .

## <a name="platform-conflicts-with-rid"></a>Conflits de plateforme avec RID

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** L’application’MACHINE/WEBROOT/APPHOST/{ASSEMBLy} 'avec la racine physique’C : \{ path} \' n’a pas pu démarrer le processus avec la ligne de commande' "c : \{ path} {assembly}. { exe | dll} "', ErrorCode = ' 0x80004005 : FF.

* **Journal stdout du Module ASP.net Core :** Exception non gérée : System. BadImageFormatException : impossible de charger le fichier ou l’assembly' {ASSEMBLy}. dll'. Tentative de chargement d’un programme au format incorrect.

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d'informations, consultez <xref:test/troubleshoot-azure-iis>.

* Si cette exception se produit pour un déploiement d’applications Azure pendant la mise à niveau d’une application et le déploiement de nouveaux assemblys, supprimez manuellement tous les fichiers du déploiement précédent. Le fait de laisser des assemblys incompatibles peut provoquer une exception `System.BadImageFormatException` lors du déploiement d’une application mise à niveau.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Point de terminaison d’URI incorrect ou site web arrêté

* **Navigateur :** ERR_CONNECTION_REFUSED **--ou--** impossible de se connecter

* **Journal des applications :** Aucune entrée

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

* **Journal de débogage du Module ASP.net Core :** Le fichier journal n’est pas créé.

Résolution des problèmes :

* Vérifiez que le point de terminaison d’URI approprié de l’application est en cours d’utilisation. Vérifiez les liaisons.

* Vérifiez que le site web IIS n’est pas à l’état *Arrêté* .

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Fonctionnalités de serveur W3SVC ou CoreWebEngine désactivées

**Exception de système d’exploitation :** Les fonctionnalités CoreWebEngine et W3SVC d’IIS 7.0 doivent être installées pour utiliser le Module ASP.NET Core.

Résolution des problèmes :

Vérifiez que le rôle et les fonctionnalités appropriés sont activés. Consultez [Configuration d’IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Chemin physique de site web incorrect ou application manquante

* **Navigateur :** 403 - Interdit : accès refusé **--ou--** 403.14 - Interdit : Le serveur Web est configuré pour ne pas afficher le contenu de ce répertoire.

* **Journal des applications :** Aucune entrée

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

* **Journal de débogage du Module ASP.net Core :** Le fichier journal n’est pas créé.

Résolution des problèmes :

Consultez les **Paramètres de base** du site web IIS et le dossier d’application physique. Vérifiez que l’application est dans le dossier sur le **chemin physique** du site web IIS.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Rôle incorrect, module ASP.NET Core non installé ou autorisations incorrectes

* **Navigateur :** 500.19 Erreur interne du serveur : Impossible d’accéder à la page que vous avez demandée, car les données de configuration connexes relatives à la page ne sont pas valides. **--OU--** Impossible d’afficher cette page

* **Journal des applications :** Aucune entrée

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

* **Journal de débogage du Module ASP.net Core :** Le fichier journal n’est pas créé.

Résolution des problèmes :

* Vérifiez que le rôle approprié est activé. Consultez [Configuration d’IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Ouvrez **Programmes et fonctionnalités** ou **Applications et fonctionnalités** , puis vérifiez que **Windows Server Hosting** est installé. Si **Windows Server Hosting** ne figure pas dans la liste des programmes installés, téléchargez et installez le bundle d’hébergement .NET Core.

  [Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Pour plus d’informations, consultez [Installer le bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Assurez-vous **Application Pool** que le > **modèle de processus** du pool > **Identity** d’applications est défini sur **Identity ApplicationPool** ou que l’identité personnalisée dispose des autorisations appropriées pour accéder au dossier de déploiement de l’application.

* Si vous avez désinstallé le bundle d’hébergement ASP.NET Core et installé une version antérieure du bundle d’hébergement, le fichier *applicationHost.config* ne contient pas de section pour le module ASP.NET Core. Ouvrez *applicationHost.config* sur *%windir%/System32/inetsrv/config* et recherchez le groupe de sections `<configuration><configSections><sectionGroup name="system.webServer">`. Si la section pour le module ASP.NET Core ne se trouve pas dans le groupe de sections, ajoutez l’élément de section :

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Sinon, installez la dernière version du bundle d’hébergement ASP.NET Core. La dernière version est rétrocompatible avec les applications ASP.NET Core prises en charge.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>processPath incorrect, variable de chemin manquante, bundle d’hébergement non installé, système/IIS non redémarré, VC++ Redistributable non installé ou violation d’accès dotnet.exe

* **Navigateur :** Erreur HTTP 500,0-échec du chargement du gestionnaire de In-Process ANCM

* **Journal des applications :** L’application’MACHINE/WEBROOT/APPHOST/{ASSEMBLy} 'avec la racine physique’C : \{ path} \' n’a pas pu démarrer le processus avec la ligne de commande' "{...}" ', ErrorCode = ' 0x80070002:0. L’application « {PATH} » n’a pas pu démarrer. L’exécutable est introuvable sur « {PATH} ». Échec du démarrage de l’application « /LM/W3SVC/2/ROOT ». Code d’erreur : 0x8007023e.

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

* **Journal de débogage du Module ASP.net Core :** Journal des événements : «l’application' {PATH} 'n’a pas pu démarrer. L’exécutable est introuvable sur « {PATH} ». Échec de HRESULT retourné : 0x8007023e

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d'informations, consultez <xref:test/troubleshoot-azure-iis>.

* Examinez l’attribut *processPath* de l’élément `<aspNetCore>` dans *web.config* afin de vérifier qu’il s’agit de `dotnet` pour un déploiement dépendant du framework ou de `.\{ASSEMBLY}.exe` pour un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd).

* Pour un déploiement dépendant du framework, *dotnet.exe* peut ne pas être accessible via les paramètres PATH. Vérifiez que *C:\Program Files\dotnet\\* existe dans les paramètres PATH du système.

* Dans le cas d’un déploiement dépendant du framework, *dotnet.exe* risque de ne pas être accessible pour l’identité de l’utilisateur du pool d’applications. Vérifiez que l’identité de l’utilisateur du pool d’applications a accès au répertoire *C:\Program Files\dotnet* . Vérifiez qu’aucune règle de refus d’accès n’est configurée pour l’identité de l’utilisateur du pool d’applications sur les répertoires *C:\Program Files\dotnet* et d’application.

* Peut-être qu’un déploiement dépendant du framework a été déployé et que .NET Core a été installé sans redémarrage d’IIS. Redémarrez le serveur ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

* Peut-être qu’un déploiement dépendant du framework a été déployé sans que le runtime .NET Core soit installé sur le système hôte. Si le runtime .NET Core n’a pas été installé, exécutez le **programme d’installation du bundle d’hébergement .NET Core** sur le système.

  [Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Pour plus d’informations, consultez [Installer le bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Si un runtime spécifique est nécessaire, téléchargez-le à partir des [archives de téléchargement .NET](https://dotnet.microsoft.com/download/archives), puis installez-le sur le système. Terminez l’installation en redémarrant le système ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Arguments incorrects de l' \<aspNetCore> élément

* **Navigateur :** Erreur HTTP 500,0-échec du chargement du gestionnaire de In-Process ANCM

* **Journal des applications :** Échec de l’appel de hostfxr pour rechercher le gestionnaire de demandes InProcess sans rechercher de dépendances natives. Cela signifie très probablement que l’application est mal configurée. Vérifiez les versions de Microsoft.NetCore.App et Microsoft.AspNetCore.App ciblées par l’application et installées sur la machine. Le gestionnaire de requêtes in-process est introuvable. Sortie capturée de l’appel de hostfxr : souhaitiez-vous exécuter des commandes du kit de développement logiciel (SDK) dotnet ? Installez le kit de développement logiciel (SDK) dotnet à partir de : https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 échec du démarrage de l’application « /LM/W3SVC/3/root », ErrorCode « 0x8000FFFF ».

* **Journal stdout du Module ASP.net Core :** Souhaitiez-vous exécuter des commandes du kit de développement logiciel (SDK) dotnet ? Installez le kit SDK dotnet à partir de : https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **Journal de débogage du Module ASP.net Core :** Échec de l’appel de hostfxr pour rechercher le gestionnaire de demandes InProcess sans rechercher de dépendances natives. Cela signifie très probablement que l’application est mal configurée. Vérifiez les versions de Microsoft.NetCore.App et Microsoft.AspNetCore.App ciblées par l’application et installées sur la machine. Échec de HRESULT retourné : 0x8000FFFF n’a pas pu trouver le gestionnaire de demandes InProcess. Sortie capturée de l’appel de hostfxr : souhaitiez-vous exécuter des commandes du kit de développement logiciel (SDK) dotnet ? Installez le kit de développement logiciel (SDK) dotnet à partir de : https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 échec de HRESULT retourné : 0x8000FFFF

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d'informations, consultez <xref:test/troubleshoot-azure-iis>.

* Examinez l’attribut *arguments* de l’élément `<aspNetCore>` dans *web.config* afin de vérifier (a) qu’il s’agit de `.\{ASSEMBLY}.dll` pour un déploiement dépendant du framework, ou (b) qu’il est absent ou qu’il s’agit d’une chaîne vide (`arguments=""`) ou d’une liste d’arguments de l’application (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) pour un déploiement autonome.

## <a name="missing-net-core-shared-framework"></a>Framework partagé .NET Core manquant

* **Navigateur :** Erreur HTTP 500,0-échec du chargement du gestionnaire de In-Process ANCM

* **Journal des applications :** Échec de l’appel de hostfxr pour rechercher le gestionnaire de demandes InProcess sans rechercher de dépendances natives. Cela signifie très probablement que l’application est mal configurée. Vérifiez les versions de Microsoft.NetCore.App et Microsoft.AspNetCore.App ciblées par l’application et installées sur la machine. Le gestionnaire de requêtes in-process est introuvable. Sortie capturée de l’appel de hostfxr : il n’a pas été possible de trouver une version de Framework compatible. Le framework spécifié « Microsoft.AspNetCore.App », version « {VERSION} » est introuvable.

Échec du démarrage de l’application « /LM/W3SVC/5/ROOT ». Code d’erreur : 0x8000ffff.

* **Journal stdout du Module ASP.net Core :** Il n’était pas possible de trouver une version de Framework compatible. Le framework spécifié « Microsoft.AspNetCore.App », version « {VERSION} » est introuvable.

* **Journal de débogage du Module ASP.net Core :** Échec de HRESULT retourné : 0x8000FFFF

Résolution des problèmes :

Pour un déploiement dépendant du framework, vérifiez que le runtime approprié est installé sur le système.

## <a name="stopped-application-pool"></a>Pool d’applications arrêté

* **Navigateur :** 503 Service non disponible

* **Journal des applications :** Aucune entrée

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

* **Journal de débogage du Module ASP.net Core :** Le fichier journal n’est pas créé.

Résolution des problèmes :

Vérifiez que le pool d’applications n’est pas à l’état *Arrêté* .

## <a name="sub-application-includes-a-handlers-section"></a>La sous-application contient une \<handlers> section

* **Navigateur :** Erreur HTTP 500.19 : Erreur interne du serveur

* **Journal des applications :** Aucune entrée

* **Journal stdout du Module ASP.net Core :** Le fichier journal de l’application racine est créé et affiche un fonctionnement normal. Le fichier journal de la sous-application n’est pas créé.

* **Journal de débogage du Module ASP.net Core :** Le fichier journal de l’application racine est créé et affiche un fonctionnement normal. Le fichier journal de la sous-application n’est pas créé.

Résolution des problèmes :

Vérifiez que le fichier *web.config* de la sous-application n’inclut pas de section `<handlers>` ou que la sous-application n’hérite pas des gestionnaires de l’application parente.

La section `<system.webServer>` de l’application parente de *web.config* est placée à l’intérieur d’un élément `<location>`. La <xref:System.Configuration.SectionInformation.InheritInChildApplications*> propriété a la valeur `false` pour indiquer que les paramètres spécifiés dans l' [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) élément ne sont pas hérités par les applications qui résident dans un sous-répertoire de l’application parente. Pour plus d'informations, consultez <xref:host-and-deploy/aspnet-core-module>.

## <a name="stdout-log-path-incorrect"></a>Chemin du journal stdout incorrect

* **Navigateur :** l’application répond normalement.

* **Journal des applications :** Impossible de démarrer la redirection stdout dans C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Message d’exception : HRESULT 0x80070005 renvoyé à {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp : 84. Impossible d’arrêter la redirection de stdout dans C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Message d’exception : HRESULT 0x80070002 retourné à {PATH}. Impossible de démarrer la redirection de stdout dans {CHEMIN}\aspnetcorev2_inprocess.dll.

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

* **Journal de débogage du Module ASP.net Core :** Impossible de démarrer la redirection stdout dans C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Message d’exception : HRESULT 0x80070005 renvoyé à {PATH} \aspnetcoremodulev2\commonlib\fileoutputmanager.cpp : 84. Impossible d’arrêter la redirection de stdout dans C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll. Message d’exception : HRESULT 0x80070002 retourné à {PATH}. Impossible de démarrer la redirection de stdout dans {CHEMIN}\aspnetcorev2_inprocess.dll.

Résolution des problèmes :

* Le chemin `stdoutLogFile` spécifié dans l’élément `<aspNetCore>` de *web.config* n’existe pas. Pour plus d’informations, consultez [ASP.net Core Module : création et redirection de journaux](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* L’utilisateur du pool d’applications ne dispose pas d’un accès en écriture sur le chemin du journal stdout.

## <a name="application-configuration-general-issue"></a>Problème général lié à la configuration d’application

* **Navigateur :** Erreur HTTP 500,0-ANCM échec de chargement du gestionnaire **de In-Process--ou--** erreur HTTP 500,30-ANCM In-Process échec du démarrage

* **Journal des applications :** Variable

* **Journal stdout du Module ASP.net Core :** Le fichier journal est créé, mais vide ou créé avec des entrées normales jusqu’à ce que le point de l’application échoue.

* **Journal de débogage du Module ASP.net Core :** Variable

Résolution des problèmes :

Le processus n’a pas pu démarrer, probablement en raison d’un problème de configuration ou de programmation d’application.

Pour plus d'informations, consultez les rubriques suivantes :

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Cette rubrique décrit les erreurs courantes et fournit des conseils de dépannage pour les erreurs spécifiques lors de l’hébergement d’applications ASP.NET Core sur Azure Apps service et IIS.

Pour obtenir des instructions générales sur la résolution des problèmes, consultez <xref:test/troubleshoot-azure-iis> .

Collectez les informations suivantes :

* Comportement du navigateur (code d’état et message d’erreur)
* Entrées du journal des événements de l’application
  * Azure App Service : consultez <xref:test/troubleshoot-azure-iis> .
  * IIS
    1. Sélectionnez **Démarrer** dans le menu **Windows** , tapez *Observateur d’événements* , puis appuyez sur **Entrée** .
    1. Une fois l’ **Observateur d’événements** ouvert, développez **Journaux Windows** > **Application** dans la barre latérale.
* Entrées de journal stdout et de débogage du module ASP.NET Core
  * Azure App Service : consultez <xref:test/troubleshoot-azure-iis> .
  * IIS : suivez les instructions des sections [création de journal et redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) et [journaux de diagnostic améliorés](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) de la rubrique du module ASP.net core.

Comparez les informations d’erreur aux erreurs courantes suivantes. Si vous trouvez une correspondance, suivez les conseils de dépannage.

La liste d’erreurs de cette rubrique n’est pas exhaustive. Si vous rencontrez une erreur non listée ici, ouvrez un nouveau problème à l’aide du bouton de **Commentaires sur le contenu** situé au bas de cette rubrique, puis fournissez des instructions détaillées sur la manière de reproduire l’erreur.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>La mise à niveau du système d’exploitation a supprimé le Module ASP.NET Core 32 bits

**Journal des applications :** Le fichier DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** du module n’a pas pu se charger. Les données sont erronées.

Résolution des problèmes :

Les fichiers autres que les fichiers de système d’exploitation dans le répertoire **C:\Windows\SysWOW64\inetsrv** ne sont pas conservés pendant la mise à niveau du système d’exploitation. Si le module ASP.NET Core est installé avant la mise à niveau d’un système d’exploitation et si un pool d’applications est exécuté en mode 32 bits après la mise à niveau du système d’exploitation, ce problème se produit. Après une mise à niveau du système d’exploitation, réparez le Module ASP.NET Core. Consultez [installer le bundle d’hébergement .net Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Sélectionnez **Réparer** quand le programme d’installation est exécuté.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Extension de site manquante, extensions de site 32 bits (x86) et 64 bits (x64) installées ou nombre de bits de processus incorrect défini

*S’applique aux applications hébergées par Azure App Services.*

* **Navigateur :** Erreur HTTP 500,0-échec du chargement du gestionnaire de In-Process ANCM

* **Journal des applications :** Échec de l’appel de hostfxr pour rechercher le gestionnaire de demandes InProcess sans rechercher de dépendances natives. Le gestionnaire de requêtes in-process est introuvable. Sortie capturée de l’appel de hostfxr : il n’a pas été possible de trouver une version de Framework compatible. Le framework spécifié « Microsoft.AspNetCore.App », version « {VERSION}-preview-\* » est introuvable. Échec du démarrage de l’application « /LM/W3SVC/1416782824/ROOT ». Code d’erreur : 0x8000ffff.

* **Journal stdout du Module ASP.net Core :** Il n’était pas possible de trouver une version de Framework compatible. Le framework spécifié « Microsoft.AspNetCore.App », version « {VERSION}-preview-\* » est introuvable.

Résolution des problèmes :

* Si vous exécutez l’application sur un runtime en préversion, installez l’extension de site 32 bits (x86) **ou** 64 bits (x64) qui correspond au nombre de bits de l’application et à la version du runtime de l’application. **N’installez pas les deux extensions ou plusieurs versions du runtime de l’extension.**

  * Runtime ASP.NET Core {RUNTIME VERSION} (x86)
  * Runtime ASP.NET Core {RUNTIME VERSION} (x64)

  Redémarrez l’application. Patientez quelques secondes pour que l’application redémarre.

* Si vous exécutez l’application sur un runtime en préversion et que les deux [extensions de site](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) 32 bits (x86) et 64 bits (x64) sont installées, désinstallez l’extension de site qui ne correspond pas au nombre de bits de l’application. Après avoir supprimé l’extension de site, redémarrez l’application. Patientez quelques secondes pour que l’application redémarre.

* Si vous exécutez l’application sur un runtime en préversion et que le nombre de bits de l’extension de site correspond à celui de l’application, vérifiez que la *version du runtime* de l’extension de site en préversion correspond à la version du runtime de l’application.

* Vérifiez que la **plateforme** de l’application dans **Paramètres de l’application** correspond au nombre de bits de l’application.

Pour plus d'informations, consultez <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Une application x86 est déployée mais le pool d’applications n’est pas activé pour les applications 32 bits

* **Navigateur :** Erreur HTTP 500,30-ANCM In-Process échec du démarrage

* **Journal des applications :** L’application'/LM/W3SVC/5/ROOT’avec la racine physique' {PATH} 'a atteint une exception managée inattendue, code d’exception = ' 0xe0434352 '. Pour plus d’informations, consultez les journaux stderr. L’application « /LM/W3SVC/5/ROOT » ayant pour racine physique « {PATH} » n’a pas pu charger clr et l’application managée. Sortie prématurée du thread de travail CLR

* **Journal stdout du Module ASP.net Core :** Le fichier journal est créé, mais vide.

Ce scénario est intercepté par le kit SDK au moment de la publication d’une application autonome. Le kit SDK génère une erreur si le RID ne correspond pas à la cible de la plateforme (par exemple, un RID `win10-x64` avec `<PlatformTarget>x86</PlatformTarget>` dans le fichier projet).

Résolution des problèmes :

Pour un déploiement dépendant du framework x86 (`<PlatformTarget>x86</PlatformTarget>`), activez le pool d’applications IIS pour les applications 32 bits. Dans le Gestionnaire IIS, ouvrez les **Paramètres avancés** du pool d’applications, puis affectez à l’option **Activer les applications 32 bits** la valeur **Vrai** .

## <a name="platform-conflicts-with-rid"></a>Conflits de plateforme avec RID

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** L’application’MACHINE/WEBROOT/APPHOST/{ASSEMBLy} 'avec la racine physique’C : \{ path} \' n’a pas pu démarrer le processus avec la ligne de commande' "c : \{ path} {assembly}. { exe | dll} "', ErrorCode = ' 0x80004005 : FF.

* **Journal stdout du Module ASP.net Core :** Exception non gérée : System. BadImageFormatException : impossible de charger le fichier ou l’assembly' {ASSEMBLy}. dll'. Tentative de chargement d’un programme au format incorrect.

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d'informations, consultez <xref:test/troubleshoot-azure-iis>.

* Si cette exception se produit pour un déploiement d’applications Azure pendant la mise à niveau d’une application et le déploiement de nouveaux assemblys, supprimez manuellement tous les fichiers du déploiement précédent. Le fait de laisser des assemblys incompatibles peut provoquer une exception `System.BadImageFormatException` lors du déploiement d’une application mise à niveau.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>Point de terminaison d’URI incorrect ou site web arrêté

* **Navigateur :** ERR_CONNECTION_REFUSED **--ou--** impossible de se connecter

* **Journal des applications :** Aucune entrée

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

Résolution des problèmes :

* Vérifiez que le point de terminaison d’URI approprié de l’application est en cours d’utilisation. Vérifiez les liaisons.

* Vérifiez que le site web IIS n’est pas à l’état *Arrêté* .

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>Fonctionnalités de serveur W3SVC ou CoreWebEngine désactivées

**Exception de système d’exploitation :** Les fonctionnalités CoreWebEngine et W3SVC d’IIS 7.0 doivent être installées pour utiliser le Module ASP.NET Core.

Résolution des problèmes :

Vérifiez que le rôle et les fonctionnalités appropriés sont activés. Consultez [Configuration d’IIS](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Chemin physique de site web incorrect ou application manquante

* **Navigateur :** 403 - Interdit : accès refusé **--ou--** 403.14 - Interdit : Le serveur Web est configuré pour ne pas afficher le contenu de ce répertoire.

* **Journal des applications :** Aucune entrée

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

Résolution des problèmes :

Consultez les **Paramètres de base** du site web IIS et le dossier d’application physique. Vérifiez que l’application est dans le dossier sur le **chemin physique** du site web IIS.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Rôle incorrect, module ASP.NET Core non installé ou autorisations incorrectes

* **Navigateur :** 500.19 Erreur interne du serveur : Impossible d’accéder à la page que vous avez demandée, car les données de configuration connexes relatives à la page ne sont pas valides. **--OU--** Impossible d’afficher cette page

* **Journal des applications :** Aucune entrée

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

Résolution des problèmes :

* Vérifiez que le rôle approprié est activé. Consultez [Configuration d’IIS](xref:host-and-deploy/iis/index#iis-configuration).

* Ouvrez **Programmes et fonctionnalités** ou **Applications et fonctionnalités** , puis vérifiez que **Windows Server Hosting** est installé. Si **Windows Server Hosting** ne figure pas dans la liste des programmes installés, téléchargez et installez le bundle d’hébergement .NET Core.

  [Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Pour plus d’informations, consultez [Installer le bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Assurez-vous **Application Pool** que le > **modèle de processus** du pool > **Identity** d’applications est défini sur **Identity ApplicationPool** ou que l’identité personnalisée dispose des autorisations appropriées pour accéder au dossier de déploiement de l’application.

* Si vous avez désinstallé le bundle d’hébergement ASP.NET Core et installé une version antérieure du bundle d’hébergement, le fichier *applicationHost.config* ne contient pas de section pour le module ASP.NET Core. Ouvrez *applicationHost.config* sur *%windir%/System32/inetsrv/config* et recherchez le groupe de sections `<configuration><configSections><sectionGroup name="system.webServer">`. Si la section pour le module ASP.NET Core ne se trouve pas dans le groupe de sections, ajoutez l’élément de section :

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Sinon, installez la dernière version du bundle d’hébergement ASP.NET Core. La dernière version est rétrocompatible avec les applications ASP.NET Core prises en charge.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>processPath incorrect, variable de chemin manquante, bundle d’hébergement non installé, système/IIS non redémarré, VC++ Redistributable non installé ou violation d’accès dotnet.exe

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** L’application’MACHINE/WEBROOT/APPHOST/{ASSEMBLy} 'avec la racine physique’C : \{ path} \' n’a pas pu démarrer le processus avec la ligne de commande' "{...}" ', ErrorCode = ' 0x80070002:0.

* **Journal stdout du Module ASP.net Core :** Le fichier journal est créé, mais vide.

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d'informations, consultez <xref:test/troubleshoot-azure-iis>.

* Examinez l’attribut *processPath* de l’élément `<aspNetCore>` dans *web.config* afin de vérifier qu’il s’agit de `dotnet` pour un déploiement dépendant du framework ou de `.\{ASSEMBLY}.exe` pour un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd).

* Pour un déploiement dépendant du framework, *dotnet.exe* peut ne pas être accessible via les paramètres PATH. Vérifiez que *C:\Program Files\dotnet\\* existe dans les paramètres PATH du système.

* Dans le cas d’un déploiement dépendant du framework, *dotnet.exe* risque de ne pas être accessible pour l’identité de l’utilisateur du pool d’applications. Vérifiez que l’identité de l’utilisateur du pool d’applications a accès au répertoire *C:\Program Files\dotnet* . Vérifiez qu’aucune règle de refus d’accès n’est configurée pour l’identité de l’utilisateur du pool d’applications sur les répertoires *C:\Program Files\dotnet* et d’application.

* Peut-être qu’un déploiement dépendant du framework a été déployé et que .NET Core a été installé sans redémarrage d’IIS. Redémarrez le serveur ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

* Peut-être qu’un déploiement dépendant du framework a été déployé sans que le runtime .NET Core soit installé sur le système hôte. Si le runtime .NET Core n’a pas été installé, exécutez le **programme d’installation du bundle d’hébergement .NET Core** sur le système.

  [Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Pour plus d’informations, consultez [Installer le bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Si un runtime spécifique est nécessaire, téléchargez-le à partir des [archives de téléchargement .NET](https://dotnet.microsoft.com/download/archives), puis installez-le sur le système. Terminez l’installation en redémarrant le système ou IIS en exécutant **net stop was /y** suivi de **net start w3svc** à partir d’une invite de commandes.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Arguments incorrects de l' \<aspNetCore> élément

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** L’application’MACHINE/WEBROOT/APPHOST/{ASSEMBLy} 'avec la racine physique’C : \{ path} \' n’a pas pu démarrer le processus avec la ligne de commande' « dotnet ». \{ ASSEMBLy}. dll', ErrorCode = ' 0x80004005:80008081.

* **Journal stdout du Module ASP.net Core :** L’application à exécuter n’existe pas : 'PATH \{ assembly}. dll'

Résolution des problèmes :

* Vérifiez que l’application s’exécute localement sur Kestrel. Un échec de processus peut être dû à un problème au niveau de l’application. Pour plus d'informations, consultez <xref:test/troubleshoot-azure-iis>.

* Examinez l’attribut *arguments* de l’élément `<aspNetCore>` dans *web.config* afin de vérifier (a) qu’il s’agit de `.\{ASSEMBLY}.dll` pour un déploiement dépendant du framework, ou (b) qu’il est absent ou qu’il s’agit d’une chaîne vide (`arguments=""`) ou d’une liste d’arguments de l’application (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) pour un déploiement autonome.

Résolution des problèmes :

Pour un déploiement dépendant du framework, vérifiez que le runtime approprié est installé sur le système.

## <a name="stopped-application-pool"></a>Pool d’applications arrêté

* **Navigateur :** 503 Service non disponible

* **Journal des applications :** Aucune entrée

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

Résolution des problèmes :

Vérifiez que le pool d’applications n’est pas à l’état *Arrêté* .

## <a name="sub-application-includes-a-handlers-section"></a>La sous-application contient une \<handlers> section

* **Navigateur :** Erreur HTTP 500.19 : Erreur interne du serveur

* **Journal des applications :** Aucune entrée

* **Journal stdout du Module ASP.net Core :** Le fichier journal de l’application racine est créé et affiche un fonctionnement normal. Le fichier journal de la sous-application n’est pas créé.

Résolution des problèmes :

Vérifiez que le fichier *web.config* de la sous-application n’inclut pas de section `<handlers>`.

## <a name="stdout-log-path-incorrect"></a>Chemin du journal stdout incorrect

* **Navigateur :** l’application répond normalement.

* **Journal des applications :** AVERTISSEMENT : impossible de créer stdoutLogFile \\ ? \{ PATH} \ path_doesnt_exist \ stdout_ {ID de processus} _ {TIMESTAMP}. log, ErrorCode =-2147024893.

* **Journal stdout du Module ASP.net Core :** Le fichier journal n’est pas créé.

Résolution des problèmes :

* Le chemin `stdoutLogFile` spécifié dans l’élément `<aspNetCore>` de *web.config* n’existe pas. Pour plus d’informations, consultez [ASP.net Core Module : création et redirection de journaux](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* L’utilisateur du pool d’applications ne dispose pas d’un accès en écriture sur le chemin du journal stdout.

## <a name="application-configuration-general-issue"></a>Problème général lié à la configuration d’application

* **Navigateur :** Erreur HTTP 502.5 - Échec du processus

* **Journal des applications :** L’application’MACHINE/WEBROOT/APPHOST/{ASSEMBLy} 'avec la racine physique’C : \{ chemin} a \' créé le processus avec la ligne de commande' "c : \{ chemin} \{ assembly}. { exe | dll} « », mais s’est bloqué ou n’a pas répondu ou n’a pas écouté le port donné «{PORT} », ErrorCode = « {CODE d’erreur} »

* **Journal stdout du Module ASP.net Core :** Le fichier journal est créé, mais vide.

Résolution des problèmes :

Le processus n’a pas pu démarrer, probablement en raison d’un problème de configuration ou de programmation d’application.

Pour plus d'informations, consultez les rubriques suivantes :

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
