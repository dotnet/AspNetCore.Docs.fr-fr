---
title: Résoudre les problèmes de ASP.NET Core sur Azure App Service et IIS
author: rick-anderson
description: Découvrez comment diagnostiquer les problèmes liés aux déploiements Azure App Service et Internet Information Services (IIS) des applications ASP.NET Core.
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
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 3f6b74ee9621529943d9f685d803837e7df10cfc
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589567"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a>Résoudre les problèmes de ASP.NET Core sur Azure App Service et IIS

Par [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

Cet article fournit des informations sur les erreurs de démarrage d’application courantes et des instructions sur la façon de diagnostiquer les erreurs quand une application est déployée sur Azure App Service ou IIS :

[Erreurs de démarrage d’application](#app-startup-errors)  
Explique les scénarios courants de code d’état HTTP de démarrage.

[Résoudre les problèmes sur Azure App Service](#troubleshoot-on-azure-app-service)  
Fournit des conseils de dépannage pour les applications déployées sur Azure App Service.

[Résoudre les problèmes sur IIS](#troubleshoot-on-iis)  
Fournit des conseils de dépannage pour les applications déployées sur IIS ou exécutées localement sur IIS Express. Ce guide s’applique aux déploiements Windows Server et Windows Desktop.

[Effacer les caches de package](#clear-package-caches)  
Explique ce qu’il faut faire quand des packages incohérents interrompent une application lors des mises à niveau majeures ou de la modification des versions de package.

[Ressources supplémentaires](#additional-resources)  
Répertorie des rubriques supplémentaires sur la résolution des problèmes.

## <a name="app-startup-errors"></a>Erreurs de démarrage d’application

Dans Visual Studio, un ASP.NET Core projet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) héberge par défaut pendant le débogage. Un *échec de processus 502,5* ou un *échec de démarrage de 500,30* qui se produit lorsque le débogage local peut être diagnostiqué à l’aide des conseils de cette rubrique.

### <a name="40314-forbidden"></a>403,14 interdit

L’application ne démarre pas. L’erreur suivante est enregistrée :

```
The Web server is configured to not list the contents of this directory.
```

L’erreur est généralement causée par un déploiement rompu sur le système d’hébergement, qui comprend l’un des scénarios suivants :

* L’application est déployée dans le mauvais dossier sur le système d’hébergement.
* Le processus de déploiement n’a pas réussi à déplacer tous les fichiers et dossiers de l’application vers le dossier de déploiement sur le système d’hébergement.
* Le fichier *web.config* est manquant dans le déploiement ou le contenu du fichier *web.config* est incorrect.

Procédez comme suit :

1. Supprimez tous les fichiers et dossiers du dossier de déploiement sur le système d’hébergement.
1. Redéployez le contenu du dossier de *publication* de l’application sur le système d’hébergement à l’aide de votre méthode de déploiement normale, telle que Visual Studio, PowerShell ou le déploiement manuel :
   * Vérifiez que le fichier *web.config* est présent dans le déploiement et que son contenu est correct.
   * Lors de l’hébergement sur Azure App Service, vérifiez que l’application est déployée dans le `D:\home\site\wwwroot` dossier.
   * Lorsque l’application est hébergée par IIS, vérifiez que l’application est déployée sur le **chemin d’accès physique** IIS indiqué dans les **paramètres de base** du gestionnaire des **services Internet**.
1. Confirmez que tous les fichiers et dossiers de l’application sont déployés en comparant le déploiement sur le système d’hébergement au contenu du dossier de *publication* du projet.

Pour plus d’informations sur la disposition d’une application ASP.NET Core publiée, consultez <xref:host-and-deploy/directory-structure> . Pour plus d’informations sur le fichier *web.config* , consultez <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .

### <a name="500-internal-server-error"></a>500 Erreur interne du serveur

L’application démarre, mais une erreur empêche le serveur de répondre à la requête.

Cette erreur se produit dans le code de l’application pendant le démarrage ou durant la création d’une réponse. La réponse peut être dépourvue de contenu ou apparaître sous la forme d’une *erreur de serveur interne 500* dans le navigateur. Le Journal des événements de l’application indique généralement qu’elle a démarré normalement. Du point de vue du serveur, c’est exact. L’application a démarré, mais elle ne peut pas générer de réponse valide. Exécutez l’application depuis une invite de commandes sur le serveur ou activez le journal stdout du module ASP.NET Core pour résoudre le problème.

### <a name="5000-in-process-handler-load-failure"></a>500.0 - Échec de chargement du gestionnaire in-process

Le processus de travail échoue. L’application ne démarre pas.

Une erreur inconnue s’est produite lors du chargement des composants du [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) . Effectuez l'une des opérations suivantes :

* Contactez le [Support Microsoft](https://support.microsoft.com/oas/default.aspx?prid=15832) (sélectionnez **Outils de développement**, puis **ASP.NET Core**).
* Posez une question sur Stack Overflow.
* Signalez un problème sur notre [dépôt GitHub](https://github.com/dotnet/AspNetCore).

### <a name="50030-in-process-startup-failure"></a>500.30 - Échec du démarrage in-process

Le processus de travail échoue. L’application ne démarre pas.

Le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) tente de démarrer le CLR .net Core in-process, mais il ne parvient pas à démarrer. Vous pouvez généralement déterminer la cause d’un échec de démarrage du processus à partir des entrées du Journal des événements de l’application et du journal stdout du module ASP.NET Core.

Conditions d’échec courantes :

* L’application n’est pas configurée en raison du ciblage d’une version du Framework partagé ASP.NET Core qui n’est pas présent. Vérifiez les versions du framework partagé ASP.NET Core qui sont installées sur l’ordinateur cible.
* À l’aide de Azure Key Vault, le manque d’autorisations au Key Vault. Vérifiez les stratégies d’accès dans le Key Vault ciblé pour vous assurer que les autorisations appropriées sont accordées.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500.31 - Échec de la recherche de dépendances natives par ANCM

Le processus de travail échoue. L’application ne démarre pas.

Le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) tente de démarrer le Runtime .net Core in-process, mais il ne parvient pas à démarrer. La cause la plus courante de cet échec de démarrage est liée à la non-installation du runtime `Microsoft.NETCore.App` ou `Microsoft.AspNetCore.App`. Si l’application est déployée pour cibler ASP.NET Core 3.0, et si cette version n’existe pas sur la machine, cette erreur se produit. Voici un exemple de message d’erreur :

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

Le message d’erreur liste toutes les versions installées de .NET Core ainsi que la version demandée par l’application. Pour corriger cette erreur, vous avez le choix entre plusieurs possibilités :

* Installez la version appropriée de .NET Core sur la machine.
* Changez l’application pour cibler une version de .NET Core présente sur la machine.
* Publiez l’application sous forme de [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd).

Durant l’exécution en phase de développement (la variable d’environnement `ASPNETCORE_ENVIRONMENT` a la valeur `Development`), l’erreur spécifique est écrite dans la réponse HTTP. La cause d’un échec de démarrage du processus se trouve également dans le Journal des événements de l’application.

### <a name="50032-ancm-failed-to-load-dll"></a>500.32 - Échec du chargement de la dll par ANCM

Le processus de travail échoue. L’application ne démarre pas.

Le plus souvent, cette erreur provient du fait que l’application est publiée pour une architecture de processeur incompatible. Si le processus de travail s’exécute en tant qu’application 32 bits et si l’application a été publiée pour cibler une architecture 64 bits, cette erreur se produit.

Pour corriger cette erreur, vous avez le choix entre plusieurs possibilités :

* Republiez l’application pour la même architecture de processeur que celle du processus de travail.
* Publiez l’application sous forme de [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-executables-fde).

### <a name="50033-ancm-request-handler-load-failure"></a>500.33 - Échec du chargement du gestionnaire de requêtes ANCM

Le processus de travail échoue. L’application ne démarre pas.

L’application n’a pas référencé le framework `Microsoft.AspNetCore.App`. Seules les applications ciblant le `Microsoft.AspNetCore.App` Framework peuvent être hébergées par le [module ASP.net Core](xref:host-and-deploy/aspnet-core-module).

Pour corriger cette erreur, vérifiez que l’application cible le framework `Microsoft.AspNetCore.App`. Examinez le fichier `.runtimeconfig.json` pour vérifier le framework ciblé par l’application.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500.34 - Modèles d’hébergement mixtes ANCM non pris en charge

Le processus de travail ne peut pas exécuter à la fois une application in-process et une application out-of-process dans le même processus.

Pour corriger cette erreur, exécutez les applications dans des pools d’applications IIS distincts.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500.35 - Applications in-process multiples dans le même processus ANCM

Le processus de travail ne peut pas exécuter plusieurs applications in-process dans le même processus.

Pour corriger cette erreur, exécutez les applications dans des pools d’applications IIS distincts.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500.36 - Échec de chargement du gestionnaire out-of-process ANCM

Le gestionnaire de requêtes out-of-process, *aspnetcorev2_outofprocess.dll*, ne se trouve pas aux côtés du fichier *aspnetcorev2.dll*. Cela indique une installation endommagée du [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module).

Pour corriger cette erreur, réparez l’installation du [bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pour IIS) ou Visual Studio (pour IIS Express).

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500.37 - Échec du démarrage d’ANCM dans le délai imparti

ANCM n’a pas pu démarrer dans le délai imparti pour le démarrage. Par défaut, le délai d’expiration est de 120 secondes.

Cette erreur peut se produire quand un grand nombre d’applications démarrent sur la même machine. Recherchez les pics d’utilisation du processeur/de la mémoire sur le serveur durant le démarrage. Vous devrez peut-être décaler le processus de démarrage de plusieurs applications.

### <a name="50038-ancm-application-dll-not-found"></a>500,38 DLL d’application ANCM introuvable

ANCM n’a pas pu localiser la DLL de l’application, qui doit être à côté de l’exécutable.

Cette erreur se produit lors de l’hébergement d’une application empaquetée en tant qu' [exécutable à fichier unique](/dotnet/core/whats-new/dotnet-core-3-0#single-file-executables) à l’aide du modèle d’hébergement in-process. Le modèle in-process requiert que le ANCM charge l’application .NET Core dans le processus IIS existant. Ce scénario n’est pas pris en charge par le modèle de déploiement à fichier unique. Utilisez l' **une** des approches suivantes dans le fichier projet de l’application pour corriger cette erreur :

1. Désactivez la publication sur un seul fichier en affectant `PublishSingleFile` à la propriété MSBuild la valeur `false` .
1. Basculez vers le modèle d’hébergement out-of-process en affectant `AspNetCoreHostingModel` à la propriété MSBuild la valeur `OutOfProcess` .

### <a name="5025-process-failure"></a>502.5 échec du processus

Le processus de travail échoue. L’application ne démarre pas.

Le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tente, en vain, de démarrer le processus de travail. Vous pouvez généralement déterminer la cause d’un échec de démarrage du processus à partir des entrées du Journal des événements de l’application et du journal stdout du module ASP.NET Core.

Une condition d’échec courante est une application mal configurée qui cible une version du framework partagé ASP.NET Core non présente. Vérifiez les versions du framework partagé ASP.NET Core qui sont installées sur l’ordinateur cible. Le *framework partagé* est le jeu d’assemblys (fichiers *.dll*) qui sont installés sur l’ordinateur et référencés par un métapaquet comme `Microsoft.AspNetCore.App`. La référence de métapaquet peut spécifier une version minimale requise. Pour plus d’informations, consultez [Le framework partagé](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

La page d’erreur d’un *échec de processus 502.5* est retournée quand une erreur de configuration d’hébergement ou d’application entraîne l’échec du processus de travail :

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Échec du démarrage de l’application (code d’erreur « 0x800700c1 »)

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

L’application n’a pas pu démarrer, car l’assembly de l’application (*.dll*) n’a pas pu être chargé.

Cette erreur se produit lorsqu’il existe une incompatibilité au niveau du nombre de bits entre l’application publiée et le processus w3wp/iisexpress.

Vérifiez que le paramètre 32 bits du pool d’applications est correct :

1. Sélectionnez le pool d’applications parmi les **Pools d’applications** IIS Manager.
1. Sélectionnez **Paramètres avancés** sous **Modifier le pool d’applications** dans le volet **Actions**.
1. Définissez **Activer les applications 32 bits** :
   * Si vous déployez une application 32 bits (x86), définissez la valeur sur `True`.
   * Si vous déployez une application 64 bits (x64), définissez la valeur sur `False`.

Confirmez qu’il n’existe pas de conflit entre une `<Platform>` propriété MSBuild dans le fichier projet et le nombre de bits publié de l’application.

### <a name="connection-reset"></a>Réinitialisation de la connexion

Si une erreur se produit après l’envoi des en-têtes, il est trop tard pour que le serveur puisse envoyer une **erreur de serveur interne 500**. C’est souvent le cas quand une erreur se produit pendant la sérialisation des objets complexes d’une réponse. Ce type d’erreur apparaît en tant qu’erreur de *réinitialisation de la connexion* sur le client. La [journalisation des applications](xref:fundamentals/logging/index) peut aider à le résoudre.

### <a name="default-startup-limits"></a>Limites de démarrage par défaut

Le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) est configuré avec un *startupTimeLimit* par défaut de 120 secondes. Quand cette valeur par défaut est conservée, une application peut mettre jusqu’à deux minutes à démarrer avant que le module ne consigne un échec de processus. Pour plus d’informations sur la configuration du module, voir [Attributs de l’élément aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Résoudre les problèmes sur Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Journal des événements d’application (Azure App Service)

Pour accéder au Journal des événements de l’application, utilisez le panneau **Diagnostiquer et résoudre les problèmes** du Portail Azure :

1. Dans le Portail Azure, ouvrez l’application dans **App Services**.
1. Sélectionnez **Diagnostiquer et résoudre les problèmes**.
1. Sélectionnez le titre **Outils de diagnostic**.
1. Sous **Outils de support**, sélectionnez le bouton **Événements d’application**.
1. Examinez la dernière erreur fournie par l’entrée *IIS AspNetCoreModule* ou *IIS AspNetCoreModule V2* dans la colonne **Source**.

En dehors de la solution consistant à utiliser le panneau **Diagnostiquer et résoudre les problèmes**, vous pouvez examiner directement le fichier Journal des événements de l’application avec [Kudu](https://github.com/projectkudu/kudu/wiki) :

1. Ouvrez les **Outils avancés** dans la zone **Outils de développement**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
1. Ouvrez le fichier **LogFiles** .
1. Sélectionnez l’icône en forme de crayon à côté du fichier *eventlog.xml*.
1. Examinez le journal. Allez en bas du journal pour voir les événements les plus récents.

### <a name="run-the-app-in-the-kudu-console"></a>Exécuter l’application dans la console Kudu

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans le Journal des événements de l’application. Vous pouvez exécuter l’application dans la console d’exécution à distance [Kudu](https://github.com/projectkudu/kudu/wiki) pour détecter l’erreur :

1. Ouvrez les **Outils avancés** dans la zone **Outils de développement**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Tester une application 32 bits (x86)

**Version actuelle**

1. `cd d:\home\site\wwwroot`
1. Exécutez l’application :
   * Si l’application est un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) :

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) :

     ```console
     {ASSEMBLY NAME}.exe
     ```

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

**Déploiement dépendant du framework sur une préversion**

*Installation de l’extension de site du runtime ASP.NET Core {VERSION} (x86) requise.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` est la version du runtime).
1. Exécutez l’application : `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Tester une application 64 bits (x64)

**Version actuelle**

* Si l’application est un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) 64 bits (x64) :
  1. `cd D:\Program Files\dotnet`
  1. Exécutez l’application : `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) :
  1. `cd D:\home\site\wwwroot`
  1. Exécutez l’application : `{ASSEMBLY NAME}.exe`

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

**Déploiement dépendant du framework sur une préversion**

*Installation de l’extension de site du runtime ASP.NET Core {VERSION} (x64) requise.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` est la version du runtime).
1. Exécutez l’application : `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Journal stdout du module ASP.NET Core (Azure App Service)

> [!WARNING]
> Si vous ne désactivez pas le journal stdout, l’application ou le serveur risque d’échouer. Il n’existe aucune limite quant à la taille du fichier journal ou au nombre de fichiers journaux créés. N’utilisez la journalisation stdout que pour résoudre les problèmes de démarrage de l’application.
>
> Pour la journalisation générale dans une application ASP.NET Core après le démarrage, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

Le journal stdout du module ASP.NET Core enregistre souvent des messages d’erreur utiles et absents du Journal des événements de l’application. Pour activer et afficher les journaux stdout :

1. Dans le portail Azure, accédez à l’application Web.
1. Dans le panneau **app service** , entrez **Kudu** dans la zone de recherche.
1. Sélectionnez **Outils avancés** > **Go**.
1. Sélectionnez  **console de débogage > cmd**.
1. Accédez à *site/wwwroot*
1. Sélectionnez l’icône de crayon pour modifier le fichier *web.config* .
1. Dans l' `<aspNetCore />` élément, définissez `stdoutLogEnabled="true"` et sélectionnez **Enregistrer**.

Désactivez la journalisation stdout lorsque la résolution des problèmes est terminée en définissant `stdoutLogEnabled="false"` .

Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

<a name="enhanced-diagnostic-logs"></a>

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>Journal de débogage du module ASP.NET Core (Azure App Service)

Le journal de débogage du module ASP.NET Core fournit une journalisation supplémentaire, plus approfondie, à partir du module ASP.NET Core. Pour activer et afficher les journaux stdout :

1. Pour activer le journal de diagnostic amélioré, effectuez l’une des opérations suivantes :
   * Suivez les instructions indiquées dans [Journaux de diagnostic améliorés](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) afin de configurer l’application pour une journalisation des diagnostics améliorée. Redéployez l’application.
   * Ajoutez le `<handlerSettings>` présenté dans [Journaux de diagnostic améliorés](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) au fichier *web.config* de l’application en production à l’aide de la console Kudu :
     1. Ouvrez les **Outils avancés** dans la zone **Outils de développement**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
     1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
     1. Ouvrez les dossiers sur le chemin d’accès du **site**  >  **wwwroot**. Modifiez le fichier *web.config* en sélectionnant le bouton représentant un crayon. Ajoutez la section `<handlerSettings>` comme indiqué dans [Journaux de diagnostic améliorés](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Sélectionnez le bouton **Enregistrer**.
1. Ouvrez les **Outils avancés** dans la zone **Outils de développement**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
1. Ouvrez les dossiers sur le chemin d’accès du **site**  >  **wwwroot**. Si vous n’avez pas indiqué de chemin pour le fichier *aspnetcore-debug.log*, le fichier apparaît dans la liste. Si vous avez indiqué un chemin, accédez à l’emplacement du fichier journal.
1. Ouvrez le fichier journal à l’aide du bouton représentant un crayon à côté du nom de fichier.

Désactivez la journalisation du débogage, une fois la résolution des problèmes effectuée :

Pour désactiver la journalisation de débogage améliorée, effectuez l’une des opérations suivantes :

* Supprimez `<handlerSettings>` du fichier *web.config* localement, puis redéployez l’application.
* Utilisez la console Kudu pour modifier le fichier *web.config* et supprimer la section `<handlerSettings>`. Enregistrez le fichier .

Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Si le journal de débogage n’est pas désactivé, cela peut entraîner une défaillance de l’application ou du serveur. Il n’existe aucune limite à la taille du fichier journal. Utilisez uniquement la journalisation de débogage pour résoudre les problèmes de démarrage d’application.
>
> Pour la journalisation générale dans une application ASP.NET Core après le démarrage, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Application lente ou suspendue (Azure App Service)

Si une application répond lentement ou se bloque sur une requête, voir les articles suivants :

* [Résoudre les problèmes de baisse de performances d’une application web dans Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Utiliser l’extension de site Outil de diagnostic des incidents pour capturer une image mémoire des problèmes d’exception intermittente ou de performances sur Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Panneaux Monitoring

Les panneaux Monitoring offrent une expérience de résolution des erreurs différente des méthodes décrites précédemment dans la rubrique. Ils peuvent servir à diagnostiquer les erreurs de la série 500.

Vérifiez que les extensions ASP.NET Core sont installées. Si ce n’est pas le cas, installez-les manuellement :

1. Dans la section du panneau **OUTILS DE DÉVELOPPEMENT**, sélectionnez le panneau **Extensions**.
1. Les **Extensions ASP.NET Core** devraient apparaître dans la liste.
1. Si les extensions ne sont pas installées, sélectionnez le bouton **Ajouter**.
1. Choisissez les **Extensions ASP.NET Core** dans la liste.
1. Sélectionnez **OK** pour accepter les conditions légales.
1. Sélectionnez **OK** sur le panneau **Ajouter une extension**.
1. Un message contextuel d’information indique que les extensions sont bien installées.

Si la journalisation stdout n’est pas activée, suivez les étapes ci-dessous :

1. Sur le Portail Azure, sélectionnez le panneau **Outils avancés** dans la zone **OUTILS DE DÉVELOPPEMENT**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
1. Ouvrez les dossiers sur le chemin d’accès **site** > **wwwroot** et faites défiler la page jusqu’à révéler le fichier *web.config* en bas de la liste.
1. Cliquez sur l’icône en forme de crayon à côté du fichier *web.config*.
1. Définissez **stdoutLogEnabled** sur `true` et remplacez le chemin **stdoutLogFile** par : `\\?\%home%\LogFiles\stdout`.
1. Sélectionnez **Enregistrer** pour enregistrer le fichier *web.config* mis à jour.

Maintenant, activez la journalisation des diagnostics :

1. Sur le Portail Azure, sélectionnez le panneau **Journaux de diagnostic**.
1. Basculez **Journalisation des applications (système de fichiers)** et **Messages d’erreur détaillés** sur **Activé**. Sélectionnez le bouton **Enregistrer** en haut du panneau.
1. Pour inclure le suivi des demandes ayant échoué, également appelé Mise en mémoire tampon des événements d’échec des demandes (FREB), basculez **Suivi des demandes ayant échoué** sur **Activé**.
1. Sélectionnez le panneau **Flux du journal** situé juste sous le panneau **Journaux de diagnostic** sur le portail.
1. Adressez une requête à l’application.
1. La cause de l’erreur est indiquée dans les données de flux de journal.

Veillez à désactiver la journalisation stdout une fois la résolution des problèmes effectuée.

Pour afficher les journaux de suivi des demandes ayant échoué (journaux FREB) :

1. Accédez au panneau **Diagnostiquer et résoudre les problèmes** du Portail Azure.
1. Sélectionnez **Journaux de suivi des demandes ayant échoué** dans la zone **OUTILS DE PRISE EN CHARGE** de la barre latérale.

Voir la [section Traces des demandes ayant échoué de la rubrique Activer la journalisation des diagnostics pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) et [FAQ sur les performances des applications web dans Azure : comment activer le suivi des demandes ayant échoué ?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) pour plus d’informations.

Pour plus d’informations, voir [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Si vous ne désactivez pas le journal stdout, l’application ou le serveur risque d’échouer. Il n’existe aucune limite quant à la taille du fichier journal ou au nombre de fichiers journaux créés.
>
> Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Résoudre les problèmes sur IIS

### <a name="application-event-log-iis"></a>Journal des événements d’application (IIS)

Accédez au Journal des événements de l’application :

1. Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .
1. Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.
1. Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.
1. Recherchez les erreurs liées à l’application défectueuse. Les erreurs sont signalées par une valeur *IIS AspNetCore Module* (Module AspNetCore IIS) ou *IIS Express AspNetCore Module* (Module AspNetCore IIS Express) dans la colonne *Source*.

### <a name="run-the-app-at-a-command-prompt"></a>Exécuter l’application depuis une invite de commandes

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans le Journal des événements de l’application. Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.

#### <a name="framework-dependent-deployment"></a>Déploiement dépendant du framework

Si l’application est un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) :

1. Depuis une invite de commandes, accédez au dossier de déploiement et exécutez l’application en exécutant l’assembly de l’application avec *dotnet.exe*. Dans la commande suivante, remplacez le nom de l’assembly de l’application par \<assembly_name> : `dotnet .\<assembly_name>.dll` .
1. La sortie de console de l’application est écrite dans la fenêtre de console, affichant toutes les erreurs éventuelles.
1. Si les erreurs se produisent pendant qu’une requête est adressée à l’application, effectuez une requête en direction de l’hôte et du port sur lequel Kestrel écoute. À l’aide de l’hôte et du port par défaut, faites une requête en direction de `http://localhost:5000/`. Si l’application répond normalement à l’adresse de point de terminaison Kestrel, le problème est probablement lié à la configuration de l’hébergement plutôt qu’à l’application.

#### <a name="self-contained-deployment"></a>Déploiement autonome

Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) :

1. Depuis une invite de commandes, accédez au dossier de déploiement et exécutez l’exécutable de l’application. Dans la commande suivante, remplacez le nom de l’assembly de l’application par \<assembly_name> : `<assembly_name>.exe` .
1. La sortie de console de l’application est écrite dans la fenêtre de console, affichant toutes les erreurs éventuelles.
1. Si les erreurs se produisent pendant qu’une requête est adressée à l’application, effectuez une requête en direction de l’hôte et du port sur lequel Kestrel écoute. À l’aide de l’hôte et du port par défaut, faites une requête en direction de `http://localhost:5000/`. Si l’application répond normalement à l’adresse de point de terminaison Kestrel, le problème est probablement lié à la configuration de l’hébergement plutôt qu’à l’application.

### <a name="aspnet-core-module-stdout-log-iis"></a>Journal stdout du module ASP.NET Core (IIS)

Pour activer et afficher les journaux stdout :

1. Accédez au dossier de déploiement du site sur le système hôte.
1. Si le dossier *logs* n’est pas présent, créez-le. Pour obtenir des instructions sur l’activation de MSBuild et créer le dossier *logs* dans le déploiement automatiquement, consultez la rubrique [Structure des répertoires](xref:host-and-deploy/directory-structure).
1. Modifiez le fichier *web.config*. Définissez **stdoutLogEnabled** sur `true` et faites pointer le chemin **stdoutLogFile** vers le dossier *logs* (par exemple, `.\logs\stdout`). Dans le chemin, `stdout` est le préfixe du nom du fichier journal. Un horodatage, un ID de processus et une extension de fichier sont ajoutés automatiquement quand le journal est créé. Si `stdout` est utilisé comme préfixe du nom de fichier, un fichier journal classique porte le nom *stdout_20180205184032_5412.log*.
1. Vérifiez que l’identité de votre pool d’applications dispose des autorisations d’écriture sur le dossier *logs*.
1. Enregistrez le fichier *web.config* mis à jour.
1. Adressez une requête à l’application.
1. Accédez au dossier *logs*. Recherchez et ouvrez le journal stdout le plus récent.
1. Déterminez si le journal contient des erreurs.

Désactivez la journalisation stdout, une fois les problèmes résolus :

1. Modifiez le fichier *web.config*.
1. Définissez **stdoutLogEnabled** sur `false`.
1. Enregistrez le fichier .

Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Si vous ne désactivez pas le journal stdout, l’application ou le serveur risque d’échouer. Il n’existe aucune limite quant à la taille du fichier journal ou au nombre de fichiers journaux créés.
>
> Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>Journal de débogage du module ASP.NET Core (IIS)

Ajoutez les paramètres de gestionnaire suivants au fichier *web.config* de l’application pour activer le journal de débogage du module ASP.net Core :

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Vérifiez que le chemin spécifié pour le journal existe, et que l’identité du pool d’applications dispose des autorisations en écriture pour l’emplacement.

Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Afficher la page d’exception de développeur

Vous pouvez ajouter la  [variable d’environnement `ASPNETCORE_ENVIRONMENT` au fichier web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pour exécuter l’application dans l’environnement de développement. Tant que l’environnement n’est pas substitué dans le démarrage de l’application par `UseEnvironment` sur le générateur de l’hôte, la définition de la variable d’environnement permet à la [page d’exception de développeur](xref:fundamentals/error-handling) d’apparaître quand l’application est exécutée.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

La définition de la variable d’environnement `ASPNETCORE_ENVIRONMENT` est recommandée uniquement en vue d’une utilisation sur les serveurs de préproduction et de test qui ne sont pas exposés à Internet. Supprimez la variable d’environnement du fichier *web.config* une fois la résolution des problèmes effectuée. Pour plus d’informations sur la définition des variables d’environnement dans *web.config*, consultez la section sur [l’élément enfant environmentVariables d’aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Obtenir des données à partir d’une application

Si une application est capable de répondre aux requêtes, obtenez des informations sur une requête, une connexion et d’autres informations supplémentaires à partir d’une application à l’aide de l’intergiciel en ligne terminal. Pour obtenir des informations supplémentaires ainsi qu'un code d'exemple, consultez <xref:test/troubleshoot#obtain-data-from-an-app>.

### <a name="slow-or-hanging-app-iis"></a>Application lente ou bloquée (IIS)

Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.

#### <a name="app-crashes-or-encounters-an-exception"></a>L’application cesse de fonctionner ou rencontre une exception

Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :

1. Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`. Le pool d’application doit disposer des accès d’écriture dans le dossier.
1. Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) :
   * Si l’application utilise le [modèle d’hébergement in-process](xref:host-and-deploy/iis/index#in-process-hosting-model), exécutez le script pour *w3wp.exe* :

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Si l’application utilise le [modèle d’hébergement out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), exécutez le script pour *dotnet.exe* :

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.
1. Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) :
   * Si l’application utilise le [modèle d’hébergement in-process](xref:host-and-deploy/iis/index#in-process-hosting-model), exécutez le script pour *w3wp.exe* :

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Si l’application utilise le [modèle d’hébergement out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), exécutez le script pour *dotnet.exe* :

     ```console
     .\DisableDumps dotnet.exe
     ```

Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement. Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.

> [!WARNING]
> Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>L’application se bloque, ne démarre pas ou s’exécute normalement

Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.

#### <a name="analyze-the-dump"></a>Analyser le fichier dump

Un fichier dump peut être analysé à l’aide de plusieurs approches. Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).

## <a name="clear-package-caches"></a>Effacer les caches de package

Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :

1. Supprimez les dossiers *bin* et *obj*.
1. Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.

   L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` . *NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).

1. Restaurez et regénérez le projet.
1. Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Documentation Azure

* [Application Insights pour ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Section débogage à distance des applications Web de dépanner une application Web dans Azure App Service à l’aide de Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Présentation des diagnostics Azure App Service](/azure/app-service/app-service-diagnostics)
* [Surveillance des applications dans Azure App Service](/azure/app-service/web-sites-monitor)
* [Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Résoudre les erreurs HTTP de type « 502 Passerelle incorrecte » et « 503 Service indisponible » dans vos applications web Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Résoudre les problèmes de baisse de performances d’une application web dans Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [FAQ sur les performances des applications web Azure dans Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Bac à sable des applications web Azure (limitations de l’exécution du runtime App Service)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Documentation de Visual Studio

* [Débogage à distance ASP.NET Core sur IIS dans Azure dans Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Débogage à distance ASP.NET Core sur un ordinateur IIS distant dans Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Apprenez à déboguer à l’aide de Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Documentation de Visual Studio Code

* [Débogage avec Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Cet article fournit des informations sur les erreurs de démarrage d’application courantes et des instructions sur la façon de diagnostiquer les erreurs quand une application est déployée sur Azure App Service ou IIS :

[Erreurs de démarrage d’application](#app-startup-errors)  
Explique les scénarios courants de code d’état HTTP de démarrage.

[Résoudre les problèmes sur Azure App Service](#troubleshoot-on-azure-app-service)  
Fournit des conseils de dépannage pour les applications déployées sur Azure App Service.

[Résoudre les problèmes sur IIS](#troubleshoot-on-iis)  
Fournit des conseils de dépannage pour les applications déployées sur IIS ou exécutées localement sur IIS Express. Ce guide s’applique aux déploiements Windows Server et Windows Desktop.

[Effacer les caches de package](#clear-package-caches)  
Explique ce qu’il faut faire quand des packages incohérents interrompent une application lors des mises à niveau majeures ou de la modification des versions de package.

[Ressources supplémentaires](#additional-resources)  
Répertorie des rubriques supplémentaires sur la résolution des problèmes.

## <a name="app-startup-errors"></a>Erreurs de démarrage d’application

Dans Visual Studio, un ASP.NET Core projet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) héberge par défaut pendant le débogage. Un *échec de processus 502,5* ou un *échec de démarrage de 500,30* qui se produit lorsque le débogage local peut être diagnostiqué à l’aide des conseils de cette rubrique.

### <a name="40314-forbidden"></a>403,14 interdit

L’application ne démarre pas. L’erreur suivante est enregistrée :

```
The Web server is configured to not list the contents of this directory.
```

L’erreur est généralement causée par un déploiement rompu sur le système d’hébergement, qui comprend l’un des scénarios suivants :

* L’application est déployée dans le mauvais dossier sur le système d’hébergement.
* Le processus de déploiement n’a pas réussi à déplacer tous les fichiers et dossiers de l’application vers le dossier de déploiement sur le système d’hébergement.
* Le fichier *web.config* est manquant dans le déploiement ou le contenu du fichier *web.config* est incorrect.

Procédez comme suit :

1. Supprimez tous les fichiers et dossiers du dossier de déploiement sur le système d’hébergement.
1. Redéployez le contenu du dossier de *publication* de l’application sur le système d’hébergement à l’aide de votre méthode de déploiement normale, telle que Visual Studio, PowerShell ou le déploiement manuel :
   * Vérifiez que le fichier *web.config* est présent dans le déploiement et que son contenu est correct.
   * Lors de l’hébergement sur Azure App Service, vérifiez que l’application est déployée dans le `D:\home\site\wwwroot` dossier.
   * Lorsque l’application est hébergée par IIS, vérifiez que l’application est déployée sur le **chemin d’accès physique** IIS indiqué dans les **paramètres de base** du gestionnaire des **services Internet**.
1. Confirmez que tous les fichiers et dossiers de l’application sont déployés en comparant le déploiement sur le système d’hébergement au contenu du dossier de *publication* du projet.

Pour plus d’informations sur la disposition d’une application ASP.NET Core publiée, consultez <xref:host-and-deploy/directory-structure> . Pour plus d’informations sur le fichier *web.config* , consultez <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .

### <a name="500-internal-server-error"></a>500 Erreur interne du serveur

L’application démarre, mais une erreur empêche le serveur de répondre à la requête.

Cette erreur se produit dans le code de l’application pendant le démarrage ou durant la création d’une réponse. La réponse peut être dépourvue de contenu ou apparaître sous la forme d’une *erreur de serveur interne 500* dans le navigateur. Le Journal des événements de l’application indique généralement qu’elle a démarré normalement. Du point de vue du serveur, c’est exact. L’application a démarré, mais elle ne peut pas générer de réponse valide. Exécutez l’application depuis une invite de commandes sur le serveur ou activez le journal stdout du module ASP.NET Core pour résoudre le problème.

### <a name="5000-in-process-handler-load-failure"></a>500.0 - Échec de chargement du gestionnaire in-process

Le processus de travail échoue. L’application ne démarre pas.

Le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) ne parvient pas à trouver le CLR .net Core et à rechercher le gestionnaire de demandes in-process (*aspnetcorev2_inprocess.dll*). Vérifiez les éléments suivants :

* l’application cible le package NuGet [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) ou le [métapaquet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) ;
* la version du framework partagé ASP.NET Core que l’application cible est installée sur l’ordinateur cible.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 - Échec de chargement du gestionnaire out-of-process

Le processus de travail échoue. L’application ne démarre pas.

Le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) ne parvient pas à trouver le gestionnaire de demandes d’hébergement hors processus. Vérifiez que le fichier *aspnetcorev2_outofprocess.dll* est présent dans un sous-dossier en regard de *aspnetcorev2.dll*.

### <a name="5025-process-failure"></a>502.5 échec du processus

Le processus de travail échoue. L’application ne démarre pas.

Le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tente, en vain, de démarrer le processus de travail. Vous pouvez généralement déterminer la cause d’un échec de démarrage du processus à partir des entrées du Journal des événements de l’application et du journal stdout du module ASP.NET Core.

Une condition d’échec courante est une application mal configurée qui cible une version du framework partagé ASP.NET Core non présente. Vérifiez les versions du framework partagé ASP.NET Core qui sont installées sur l’ordinateur cible. Le *framework partagé* est le jeu d’assemblys (fichiers *.dll*) qui sont installés sur l’ordinateur et référencés par un métapaquet comme `Microsoft.AspNetCore.App`. La référence de métapaquet peut spécifier une version minimale requise. Pour plus d’informations, consultez [Le framework partagé](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

La page d’erreur d’un *échec de processus 502.5* est retournée quand une erreur de configuration d’hébergement ou d’application entraîne l’échec du processus de travail :

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Échec du démarrage de l’application (code d’erreur « 0x800700c1 »)

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

L’application n’a pas pu démarrer, car l’assembly de l’application (*.dll*) n’a pas pu être chargé.

Cette erreur se produit lorsqu’il existe une incompatibilité au niveau du nombre de bits entre l’application publiée et le processus w3wp/iisexpress.

Vérifiez que le paramètre 32 bits du pool d’applications est correct :

1. Sélectionnez le pool d’applications parmi les **Pools d’applications** IIS Manager.
1. Sélectionnez **Paramètres avancés** sous **Modifier le pool d’applications** dans le volet **Actions**.
1. Définissez **Activer les applications 32 bits** :
   * Si vous déployez une application 32 bits (x86), définissez la valeur sur `True`.
   * Si vous déployez une application 64 bits (x64), définissez la valeur sur `False`.

Confirmez qu’il n’existe pas de conflit entre une `<Platform>` propriété MSBuild dans le fichier projet et le nombre de bits publié de l’application.

### <a name="connection-reset"></a>Réinitialisation de la connexion

Si une erreur se produit après l’envoi des en-têtes, il est trop tard pour que le serveur puisse envoyer une **erreur de serveur interne 500**. C’est souvent le cas quand une erreur se produit pendant la sérialisation des objets complexes d’une réponse. Ce type d’erreur apparaît en tant qu’erreur de *réinitialisation de la connexion* sur le client. La [journalisation des applications](xref:fundamentals/logging/index) peut aider à le résoudre.

### <a name="default-startup-limits"></a>Limites de démarrage par défaut

Le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) est configuré avec un *startupTimeLimit* par défaut de 120 secondes. Quand cette valeur par défaut est conservée, une application peut mettre jusqu’à deux minutes à démarrer avant que le module ne consigne un échec de processus. Pour plus d’informations sur la configuration du module, voir [Attributs de l’élément aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Résoudre les problèmes sur Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Journal des événements d’application (Azure App Service)

Pour accéder au Journal des événements de l’application, utilisez le panneau **Diagnostiquer et résoudre les problèmes** du Portail Azure :

1. Dans le Portail Azure, ouvrez l’application dans **App Services**.
1. Sélectionnez **Diagnostiquer et résoudre les problèmes**.
1. Sélectionnez le titre **Outils de diagnostic**.
1. Sous **Outils de support**, sélectionnez le bouton **Événements d’application**.
1. Examinez la dernière erreur fournie par l’entrée *IIS AspNetCoreModule* ou *IIS AspNetCoreModule V2* dans la colonne **Source**.

En dehors de la solution consistant à utiliser le panneau **Diagnostiquer et résoudre les problèmes**, vous pouvez examiner directement le fichier Journal des événements de l’application avec [Kudu](https://github.com/projectkudu/kudu/wiki) :

1. Ouvrez les **Outils avancés** dans la zone **Outils de développement**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
1. Ouvrez le fichier **LogFiles** .
1. Sélectionnez l’icône en forme de crayon à côté du fichier *eventlog.xml*.
1. Examinez le journal. Allez en bas du journal pour voir les événements les plus récents.

### <a name="run-the-app-in-the-kudu-console"></a>Exécuter l’application dans la console Kudu

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans le Journal des événements de l’application. Vous pouvez exécuter l’application dans la console d’exécution à distance [Kudu](https://github.com/projectkudu/kudu/wiki) pour détecter l’erreur :

1. Ouvrez les **Outils avancés** dans la zone **Outils de développement**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Tester une application 32 bits (x86)

**Version actuelle**

1. `cd d:\home\site\wwwroot`
1. Exécutez l’application :
   * Si l’application est un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) :

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) :

     ```console
     {ASSEMBLY NAME}.exe
     ```

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

**Déploiement dépendant du framework sur une préversion**

*Installation de l’extension de site du runtime ASP.NET Core {VERSION} (x86) requise.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` est la version du runtime).
1. Exécutez l’application : `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Tester une application 64 bits (x64)

**Version actuelle**

* Si l’application est un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) 64 bits (x64) :
  1. `cd D:\Program Files\dotnet`
  1. Exécutez l’application : `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) :
  1. `cd D:\home\site\wwwroot`
  1. Exécutez l’application : `{ASSEMBLY NAME}.exe`

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

**Déploiement dépendant du framework sur une préversion**

*Installation de l’extension de site du runtime ASP.NET Core {VERSION} (x64) requise.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` est la version du runtime).
1. Exécutez l’application : `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Journal stdout du module ASP.NET Core (Azure App Service)

Le journal stdout du module ASP.NET Core enregistre souvent des messages d’erreur utiles et absents du Journal des événements de l’application. Pour activer et afficher les journaux stdout :

1. Accédez au panneau **Diagnostiquer et résoudre les problèmes** du Portail Azure.
1. Sous **SÉLECTIONNER UNE CATÉGORIE DE PROBLÈME**, sélectionnez le bouton **Application web en panne**.
1. Sous **Solutions suggérées** > **Activer la redirection de journaux stdout**, sélectionnez le bouton **Ouvrir la console Kudu pour modifier web.config**.
1. Dans la **Console de diagnostic** Kudu, ouvrez les dossiers sur le chemin d’accès **site** > **wwwroot**. Faites défiler la page jusqu’à révéler le fichier *web.config* en bas de la liste.
1. Cliquez sur l’icône en forme de crayon à côté du fichier *web.config*.
1. Définissez **stdoutLogEnabled** sur `true` et remplacez le chemin **stdoutLogFile** par : `\\?\%home%\LogFiles\stdout`.
1. Sélectionnez **Enregistrer** pour enregistrer le fichier *web.config* mis à jour.
1. Adressez une requête à l’application.
1. Revenez au portail Azure. Sélectionnez le panneau **Outils avancés** dans la zone **OUTILS DE DÉVELOPPEMENT**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
1. Sélectionnez le dossier **LogFiles**.
1. Inspectez la colonne **Modifié** et sélectionnez l’icône en forme de crayon pour modifier le journal stdout avec la date de dernière modification.
1. Lorsque le fichier journal s’ouvre, l’erreur s’affiche.

Désactivez la journalisation stdout, une fois les problèmes résolus :

1. Dans la **Console de diagnostic** Kudu, revenez au chemin d’accès **site** > **wwwroot** pour faire apparaître le fichier *web.config*. Ouvrez à nouveau le fichier **web.config** en sélectionnant l’icône en forme de crayon.
1. Définissez **stdoutLogEnabled** sur `false`.
1. Sélectionnez **Enregistrer** pour enregistrer le fichier.

Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Si vous ne désactivez pas le journal stdout, l’application ou le serveur risque d’échouer. Il n’existe aucune limite quant à la taille du fichier journal ou au nombre de fichiers journaux créés. N’utilisez la journalisation stdout que pour résoudre les problèmes de démarrage de l’application.
>
> Pour la journalisation générale dans une application ASP.NET Core après le démarrage, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>Journal de débogage du module ASP.NET Core (Azure App Service)

Le journal de débogage du module ASP.NET Core fournit une journalisation supplémentaire, plus approfondie, à partir du module ASP.NET Core. Pour activer et afficher les journaux stdout :

1. Pour activer le journal de diagnostic amélioré, effectuez l’une des opérations suivantes :
   * Suivez les instructions indiquées dans [Journaux de diagnostic améliorés](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) afin de configurer l’application pour une journalisation des diagnostics améliorée. Redéployez l’application.
   * Ajoutez le `<handlerSettings>` présenté dans [Journaux de diagnostic améliorés](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) au fichier *web.config* de l’application en production à l’aide de la console Kudu :
     1. Ouvrez les **Outils avancés** dans la zone **Outils de développement**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
     1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
     1. Ouvrez les dossiers sur le chemin d’accès du **site**  >  **wwwroot**. Modifiez le fichier *web.config* en sélectionnant le bouton représentant un crayon. Ajoutez la section `<handlerSettings>` comme indiqué dans [Journaux de diagnostic améliorés](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs). Sélectionnez le bouton **Enregistrer**.
1. Ouvrez les **Outils avancés** dans la zone **Outils de développement**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
1. Ouvrez les dossiers sur le chemin d’accès du **site**  >  **wwwroot**. Si vous n’avez pas indiqué de chemin pour le fichier *aspnetcore-debug.log*, le fichier apparaît dans la liste. Si vous avez indiqué un chemin, accédez à l’emplacement du fichier journal.
1. Ouvrez le fichier journal à l’aide du bouton représentant un crayon à côté du nom de fichier.

Désactivez la journalisation du débogage, une fois la résolution des problèmes effectuée :

Pour désactiver la journalisation de débogage améliorée, effectuez l’une des opérations suivantes :

* Supprimez `<handlerSettings>` du fichier *web.config* localement, puis redéployez l’application.
* Utilisez la console Kudu pour modifier le fichier *web.config* et supprimer la section `<handlerSettings>`. Enregistrez le fichier .

Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Si le journal de débogage n’est pas désactivé, cela peut entraîner une défaillance de l’application ou du serveur. Il n’existe aucune limite à la taille du fichier journal. Utilisez uniquement la journalisation de débogage pour résoudre les problèmes de démarrage d’application.
>
> Pour la journalisation générale dans une application ASP.NET Core après le démarrage, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Application lente ou suspendue (Azure App Service)

Si une application répond lentement ou se bloque sur une requête, voir les articles suivants :

* [Résoudre les problèmes de baisse de performances d’une application web dans Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Utiliser l’extension de site Outil de diagnostic des incidents pour capturer une image mémoire des problèmes d’exception intermittente ou de performances sur Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Panneaux Monitoring

Les panneaux Monitoring offrent une expérience de résolution des erreurs différente des méthodes décrites précédemment dans la rubrique. Ils peuvent servir à diagnostiquer les erreurs de la série 500.

Vérifiez que les extensions ASP.NET Core sont installées. Si ce n’est pas le cas, installez-les manuellement :

1. Dans la section du panneau **OUTILS DE DÉVELOPPEMENT**, sélectionnez le panneau **Extensions**.
1. Les **Extensions ASP.NET Core** devraient apparaître dans la liste.
1. Si les extensions ne sont pas installées, sélectionnez le bouton **Ajouter**.
1. Choisissez les **Extensions ASP.NET Core** dans la liste.
1. Sélectionnez **OK** pour accepter les conditions légales.
1. Sélectionnez **OK** sur le panneau **Ajouter une extension**.
1. Un message contextuel d’information indique que les extensions sont bien installées.

Si la journalisation stdout n’est pas activée, suivez les étapes ci-dessous :

1. Sur le Portail Azure, sélectionnez le panneau **Outils avancés** dans la zone **OUTILS DE DÉVELOPPEMENT**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
1. Ouvrez les dossiers sur le chemin d’accès **site** > **wwwroot** et faites défiler la page jusqu’à révéler le fichier *web.config* en bas de la liste.
1. Cliquez sur l’icône en forme de crayon à côté du fichier *web.config*.
1. Définissez **stdoutLogEnabled** sur `true` et remplacez le chemin **stdoutLogFile** par : `\\?\%home%\LogFiles\stdout`.
1. Sélectionnez **Enregistrer** pour enregistrer le fichier *web.config* mis à jour.

Maintenant, activez la journalisation des diagnostics :

1. Sur le Portail Azure, sélectionnez le panneau **Journaux de diagnostic**.
1. Basculez **Journalisation des applications (système de fichiers)** et **Messages d’erreur détaillés** sur **Activé**. Sélectionnez le bouton **Enregistrer** en haut du panneau.
1. Pour inclure le suivi des demandes ayant échoué, également appelé Mise en mémoire tampon des événements d’échec des demandes (FREB), basculez **Suivi des demandes ayant échoué** sur **Activé**.
1. Sélectionnez le panneau **Flux du journal** situé juste sous le panneau **Journaux de diagnostic** sur le portail.
1. Adressez une requête à l’application.
1. La cause de l’erreur est indiquée dans les données de flux de journal.

Veillez à désactiver la journalisation stdout une fois la résolution des problèmes effectuée.

Pour afficher les journaux de suivi des demandes ayant échoué (journaux FREB) :

1. Accédez au panneau **Diagnostiquer et résoudre les problèmes** du Portail Azure.
1. Sélectionnez **Journaux de suivi des demandes ayant échoué** dans la zone **OUTILS DE PRISE EN CHARGE** de la barre latérale.

Voir la [section Traces des demandes ayant échoué de la rubrique Activer la journalisation des diagnostics pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) et [FAQ sur les performances des applications web dans Azure : comment activer le suivi des demandes ayant échoué ?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) pour plus d’informations.

Pour plus d’informations, voir [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Si vous ne désactivez pas le journal stdout, l’application ou le serveur risque d’échouer. Il n’existe aucune limite quant à la taille du fichier journal ou au nombre de fichiers journaux créés.
>
> Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Résoudre les problèmes sur IIS

### <a name="application-event-log-iis"></a>Journal des événements d’application (IIS)

Accédez au Journal des événements de l’application :

1. Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .
1. Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.
1. Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.
1. Recherchez les erreurs liées à l’application défectueuse. Les erreurs sont signalées par une valeur *IIS AspNetCore Module* (Module AspNetCore IIS) ou *IIS Express AspNetCore Module* (Module AspNetCore IIS Express) dans la colonne *Source*.

### <a name="run-the-app-at-a-command-prompt"></a>Exécuter l’application depuis une invite de commandes

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans le Journal des événements de l’application. Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.

#### <a name="framework-dependent-deployment"></a>Déploiement dépendant du framework

Si l’application est un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) :

1. Depuis une invite de commandes, accédez au dossier de déploiement et exécutez l’application en exécutant l’assembly de l’application avec *dotnet.exe*. Dans la commande suivante, remplacez le nom de l’assembly de l’application par \<assembly_name> : `dotnet .\<assembly_name>.dll` .
1. La sortie de console de l’application est écrite dans la fenêtre de console, affichant toutes les erreurs éventuelles.
1. Si les erreurs se produisent pendant qu’une requête est adressée à l’application, effectuez une requête en direction de l’hôte et du port sur lequel Kestrel écoute. À l’aide de l’hôte et du port par défaut, faites une requête en direction de `http://localhost:5000/`. Si l’application répond normalement à l’adresse de point de terminaison Kestrel, le problème est probablement lié à la configuration de l’hébergement plutôt qu’à l’application.

#### <a name="self-contained-deployment"></a>Déploiement autonome

Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) :

1. Depuis une invite de commandes, accédez au dossier de déploiement et exécutez l’exécutable de l’application. Dans la commande suivante, remplacez le nom de l’assembly de l’application par \<assembly_name> : `<assembly_name>.exe` .
1. La sortie de console de l’application est écrite dans la fenêtre de console, affichant toutes les erreurs éventuelles.
1. Si les erreurs se produisent pendant qu’une requête est adressée à l’application, effectuez une requête en direction de l’hôte et du port sur lequel Kestrel écoute. À l’aide de l’hôte et du port par défaut, faites une requête en direction de `http://localhost:5000/`. Si l’application répond normalement à l’adresse de point de terminaison Kestrel, le problème est probablement lié à la configuration de l’hébergement plutôt qu’à l’application.

### <a name="aspnet-core-module-stdout-log-iis"></a>Journal stdout du module ASP.NET Core (IIS)

Pour activer et afficher les journaux stdout :

1. Accédez au dossier de déploiement du site sur le système hôte.
1. Si le dossier *logs* n’est pas présent, créez-le. Pour obtenir des instructions sur l’activation de MSBuild et créer le dossier *logs* dans le déploiement automatiquement, consultez la rubrique [Structure des répertoires](xref:host-and-deploy/directory-structure).
1. Modifiez le fichier *web.config*. Définissez **stdoutLogEnabled** sur `true` et faites pointer le chemin **stdoutLogFile** vers le dossier *logs* (par exemple, `.\logs\stdout`). Dans le chemin, `stdout` est le préfixe du nom du fichier journal. Un horodatage, un ID de processus et une extension de fichier sont ajoutés automatiquement quand le journal est créé. Si `stdout` est utilisé comme préfixe du nom de fichier, un fichier journal classique porte le nom *stdout_20180205184032_5412.log*.
1. Vérifiez que l’identité de votre pool d’applications dispose des autorisations d’écriture sur le dossier *logs*.
1. Enregistrez le fichier *web.config* mis à jour.
1. Adressez une requête à l’application.
1. Accédez au dossier *logs*. Recherchez et ouvrez le journal stdout le plus récent.
1. Déterminez si le journal contient des erreurs.

Désactivez la journalisation stdout, une fois les problèmes résolus :

1. Modifiez le fichier *web.config*.
1. Définissez **stdoutLogEnabled** sur `false`.
1. Enregistrez le fichier .

Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Si vous ne désactivez pas le journal stdout, l’application ou le serveur risque d’échouer. Il n’existe aucune limite quant à la taille du fichier journal ou au nombre de fichiers journaux créés.
>
> Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="aspnet-core-module-debug-log-iis"></a>Journal de débogage du module ASP.NET Core (IIS)

Ajoutez les paramètres de gestionnaire suivants au fichier *web.config* de l’application pour activer le journal de débogage du module ASP.net Core :

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Vérifiez que le chemin spécifié pour le journal existe, et que l’identité du pool d’applications dispose des autorisations en écriture pour l’emplacement.

Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Afficher la page d’exception de développeur

Vous pouvez ajouter la  [variable d’environnement `ASPNETCORE_ENVIRONMENT` au fichier web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pour exécuter l’application dans l’environnement de développement. Tant que l’environnement n’est pas substitué dans le démarrage de l’application par `UseEnvironment` sur le générateur de l’hôte, la définition de la variable d’environnement permet à la [page d’exception de développeur](xref:fundamentals/error-handling) d’apparaître quand l’application est exécutée.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

La définition de la variable d’environnement `ASPNETCORE_ENVIRONMENT` est recommandée uniquement en vue d’une utilisation sur les serveurs de préproduction et de test qui ne sont pas exposés à Internet. Supprimez la variable d’environnement du fichier *web.config* une fois la résolution des problèmes effectuée. Pour plus d’informations sur la définition des variables d’environnement dans *web.config*, consultez la section sur [l’élément enfant environmentVariables d’aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Obtenir des données à partir d’une application

Si une application est capable de répondre aux requêtes, obtenez des informations sur une requête, une connexion et d’autres informations supplémentaires à partir d’une application à l’aide de l’intergiciel en ligne terminal. Pour obtenir des informations supplémentaires ainsi qu'un code d'exemple, consultez <xref:test/troubleshoot#obtain-data-from-an-app>.

### <a name="slow-or-hanging-app-iis"></a>Application lente ou bloquée (IIS)

Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.

#### <a name="app-crashes-or-encounters-an-exception"></a>L’application cesse de fonctionner ou rencontre une exception

Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :

1. Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`. Le pool d’application doit disposer des accès d’écriture dans le dossier.
1. Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) :
   * Si l’application utilise le [modèle d’hébergement in-process](xref:host-and-deploy/iis/index#in-process-hosting-model), exécutez le script pour *w3wp.exe* :

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Si l’application utilise le [modèle d’hébergement out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), exécutez le script pour *dotnet.exe* :

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.
1. Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) :
   * Si l’application utilise le [modèle d’hébergement in-process](xref:host-and-deploy/iis/index#in-process-hosting-model), exécutez le script pour *w3wp.exe* :

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Si l’application utilise le [modèle d’hébergement out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), exécutez le script pour *dotnet.exe* :

     ```console
     .\DisableDumps dotnet.exe
     ```

Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement. Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.

> [!WARNING]
> Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>L’application se bloque, ne démarre pas ou s’exécute normalement

Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.

#### <a name="analyze-the-dump"></a>Analyser le fichier dump

Un fichier dump peut être analysé à l’aide de plusieurs approches. Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).

## <a name="clear-package-caches"></a>Effacer les caches de package

Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :

1. Supprimez les dossiers *bin* et *obj*.
1. Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.

   L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` . *NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).

1. Restaurez et regénérez le projet.
1. Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Documentation Azure

* [Application Insights pour ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Section débogage à distance des applications Web de dépanner une application Web dans Azure App Service à l’aide de Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Présentation des diagnostics Azure App Service](/azure/app-service/app-service-diagnostics)
* [Surveillance des applications dans Azure App Service](/azure/app-service/web-sites-monitor)
* [Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Résoudre les erreurs HTTP de type « 502 Passerelle incorrecte » et « 503 Service indisponible » dans vos applications web Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Résoudre les problèmes de baisse de performances d’une application web dans Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [FAQ sur les performances des applications web Azure dans Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Bac à sable des applications web Azure (limitations de l’exécution du runtime App Service)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Documentation de Visual Studio

* [Débogage à distance ASP.NET Core sur IIS dans Azure dans Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Débogage à distance ASP.NET Core sur un ordinateur IIS distant dans Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Apprenez à déboguer à l’aide de Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Documentation de Visual Studio Code

* [Débogage avec Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Cet article fournit des informations sur les erreurs de démarrage d’application courantes et des instructions sur la façon de diagnostiquer les erreurs quand une application est déployée sur Azure App Service ou IIS :

[Erreurs de démarrage d’application](#app-startup-errors)  
Explique les scénarios courants de code d’état HTTP de démarrage.

[Résoudre les problèmes sur Azure App Service](#troubleshoot-on-azure-app-service)  
Fournit des conseils de dépannage pour les applications déployées sur Azure App Service.

[Résoudre les problèmes sur IIS](#troubleshoot-on-iis)  
Fournit des conseils de dépannage pour les applications déployées sur IIS ou exécutées localement sur IIS Express. Ce guide s’applique aux déploiements Windows Server et Windows Desktop.

[Effacer les caches de package](#clear-package-caches)  
Explique ce qu’il faut faire quand des packages incohérents interrompent une application lors des mises à niveau majeures ou de la modification des versions de package.

[Ressources supplémentaires](#additional-resources)  
Répertorie des rubriques supplémentaires sur la résolution des problèmes.

## <a name="app-startup-errors"></a>Erreurs de démarrage d’application

Dans Visual Studio, un ASP.NET Core projet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) héberge par défaut pendant le débogage. Un *échec de processus 502,5* qui se produit lorsque le débogage local peut être diagnostiqué à l’aide des conseils de cette rubrique.

### <a name="40314-forbidden"></a>403,14 interdit

L’application ne démarre pas. L’erreur suivante est enregistrée :

```
The Web server is configured to not list the contents of this directory.
```

L’erreur est généralement causée par un déploiement rompu sur le système d’hébergement, qui comprend l’un des scénarios suivants :

* L’application est déployée dans le mauvais dossier sur le système d’hébergement.
* Le processus de déploiement n’a pas réussi à déplacer tous les fichiers et dossiers de l’application vers le dossier de déploiement sur le système d’hébergement.
* Le fichier *web.config* est manquant dans le déploiement ou le contenu du fichier *web.config* est incorrect.

Procédez comme suit :

1. Supprimez tous les fichiers et dossiers du dossier de déploiement sur le système d’hébergement.
1. Redéployez le contenu du dossier de *publication* de l’application sur le système d’hébergement à l’aide de votre méthode de déploiement normale, telle que Visual Studio, PowerShell ou le déploiement manuel :
   * Vérifiez que le fichier *web.config* est présent dans le déploiement et que son contenu est correct.
   * Lors de l’hébergement sur Azure App Service, vérifiez que l’application est déployée dans le `D:\home\site\wwwroot` dossier.
   * Lorsque l’application est hébergée par IIS, vérifiez que l’application est déployée sur le **chemin d’accès physique** IIS indiqué dans les **paramètres de base** du gestionnaire des **services Internet**.
1. Confirmez que tous les fichiers et dossiers de l’application sont déployés en comparant le déploiement sur le système d’hébergement au contenu du dossier de *publication* du projet.

Pour plus d’informations sur la disposition d’une application ASP.NET Core publiée, consultez <xref:host-and-deploy/directory-structure> . Pour plus d’informations sur le fichier *web.config* , consultez <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig> .

### <a name="500-internal-server-error"></a>500 Erreur interne du serveur

L’application démarre, mais une erreur empêche le serveur de répondre à la requête.

Cette erreur se produit dans le code de l’application pendant le démarrage ou durant la création d’une réponse. La réponse peut être dépourvue de contenu ou apparaître sous la forme d’une *erreur de serveur interne 500* dans le navigateur. Le Journal des événements de l’application indique généralement qu’elle a démarré normalement. Du point de vue du serveur, c’est exact. L’application a démarré, mais elle ne peut pas générer de réponse valide. Exécutez l’application depuis une invite de commandes sur le serveur ou activez le journal stdout du module ASP.NET Core pour résoudre le problème.

### <a name="5025-process-failure"></a>502.5 échec du processus

Le processus de travail échoue. L’application ne démarre pas.

Le [module ASP.NET Core](xref:host-and-deploy/aspnet-core-module) tente, en vain, de démarrer le processus de travail. Vous pouvez généralement déterminer la cause d’un échec de démarrage du processus à partir des entrées du Journal des événements de l’application et du journal stdout du module ASP.NET Core.

Une condition d’échec courante est une application mal configurée qui cible une version du framework partagé ASP.NET Core non présente. Vérifiez les versions du framework partagé ASP.NET Core qui sont installées sur l’ordinateur cible. Le *framework partagé* est le jeu d’assemblys (fichiers *.dll*) qui sont installés sur l’ordinateur et référencés par un métapaquet comme `Microsoft.AspNetCore.App`. La référence de métapaquet peut spécifier une version minimale requise. Pour plus d’informations, consultez [Le framework partagé](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).

La page d’erreur d’un *échec de processus 502.5* est retournée quand une erreur de configuration d’hébergement ou d’application entraîne l’échec du processus de travail :

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Échec du démarrage de l’application (code d’erreur « 0x800700c1 »)

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

L’application n’a pas pu démarrer, car l’assembly de l’application (*.dll*) n’a pas pu être chargé.

Cette erreur se produit lorsqu’il existe une incompatibilité au niveau du nombre de bits entre l’application publiée et le processus w3wp/iisexpress.

Vérifiez que le paramètre 32 bits du pool d’applications est correct :

1. Sélectionnez le pool d’applications parmi les **Pools d’applications** IIS Manager.
1. Sélectionnez **Paramètres avancés** sous **Modifier le pool d’applications** dans le volet **Actions**.
1. Définissez **Activer les applications 32 bits** :
   * Si vous déployez une application 32 bits (x86), définissez la valeur sur `True`.
   * Si vous déployez une application 64 bits (x64), définissez la valeur sur `False`.

Confirmez qu’il n’existe pas de conflit entre une `<Platform>` propriété MSBuild dans le fichier projet et le nombre de bits publié de l’application.

### <a name="connection-reset"></a>Réinitialisation de la connexion

Si une erreur se produit après l’envoi des en-têtes, il est trop tard pour que le serveur puisse envoyer une **erreur de serveur interne 500**. C’est souvent le cas quand une erreur se produit pendant la sérialisation des objets complexes d’une réponse. Ce type d’erreur apparaît en tant qu’erreur de *réinitialisation de la connexion* sur le client. La [journalisation des applications](xref:fundamentals/logging/index) peut aider à le résoudre.

### <a name="default-startup-limits"></a>Limites de démarrage par défaut

Le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module) est configuré avec un *startupTimeLimit* par défaut de 120 secondes. Quand cette valeur par défaut est conservée, une application peut mettre jusqu’à deux minutes à démarrer avant que le module ne consigne un échec de processus. Pour plus d’informations sur la configuration du module, voir [Attributs de l’élément aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

## <a name="troubleshoot-on-azure-app-service"></a>Résoudre les problèmes sur Azure App Service

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Journal des événements d’application (Azure App Service)

Pour accéder au Journal des événements de l’application, utilisez le panneau **Diagnostiquer et résoudre les problèmes** du Portail Azure :

1. Dans le Portail Azure, ouvrez l’application dans **App Services**.
1. Sélectionnez **Diagnostiquer et résoudre les problèmes**.
1. Sélectionnez le titre **Outils de diagnostic**.
1. Sous **Outils de support**, sélectionnez le bouton **Événements d’application**.
1. Examinez la dernière erreur fournie par l’entrée *IIS AspNetCoreModule* ou *IIS AspNetCoreModule V2* dans la colonne **Source**.

En dehors de la solution consistant à utiliser le panneau **Diagnostiquer et résoudre les problèmes**, vous pouvez examiner directement le fichier Journal des événements de l’application avec [Kudu](https://github.com/projectkudu/kudu/wiki) :

1. Ouvrez les **Outils avancés** dans la zone **Outils de développement**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
1. Ouvrez le fichier **LogFiles** .
1. Sélectionnez l’icône en forme de crayon à côté du fichier *eventlog.xml*.
1. Examinez le journal. Allez en bas du journal pour voir les événements les plus récents.

### <a name="run-the-app-in-the-kudu-console"></a>Exécuter l’application dans la console Kudu

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans le Journal des événements de l’application. Vous pouvez exécuter l’application dans la console d’exécution à distance [Kudu](https://github.com/projectkudu/kudu/wiki) pour détecter l’erreur :

1. Ouvrez les **Outils avancés** dans la zone **Outils de développement**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.

#### <a name="test-a-32-bit-x86-app"></a>Tester une application 32 bits (x86)

**Version actuelle**

1. `cd d:\home\site\wwwroot`
1. Exécutez l’application :
   * Si l’application est un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) :

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) :

     ```console
     {ASSEMBLY NAME}.exe
     ```

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

**Déploiement dépendant du framework sur une préversion**

*Installation de l’extension de site du runtime ASP.NET Core {VERSION} (x86) requise.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32` (`{X.Y}` est la version du runtime).
1. Exécutez l’application : `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

#### <a name="test-a-64-bit-x64-app"></a>Tester une application 64 bits (x64)

**Version actuelle**

* Si l’application est un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) 64 bits (x64) :
  1. `cd D:\Program Files\dotnet`
  1. Exécutez l’application : `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) :
  1. `cd D:\home\site\wwwroot`
  1. Exécutez l’application : `{ASSEMBLY NAME}.exe`

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

**Déploiement dépendant du framework sur une préversion**

*Installation de l’extension de site du runtime ASP.NET Core {VERSION} (x64) requise.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64` (`{X.Y}` est la version du runtime).
1. Exécutez l’application : `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

La sortie de la console de l’application, qui affiche les erreurs, est dirigée vers la console Kudu.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>Journal stdout du module ASP.NET Core (Azure App Service)

Le journal stdout du module ASP.NET Core enregistre souvent des messages d’erreur utiles et absents du Journal des événements de l’application. Pour activer et afficher les journaux stdout :

1. Accédez au panneau **Diagnostiquer et résoudre les problèmes** du Portail Azure.
1. Sous **SÉLECTIONNER UNE CATÉGORIE DE PROBLÈME**, sélectionnez le bouton **Application web en panne**.
1. Sous **Solutions suggérées** > **Activer la redirection de journaux stdout**, sélectionnez le bouton **Ouvrir la console Kudu pour modifier web.config**.
1. Dans la **Console de diagnostic** Kudu, ouvrez les dossiers sur le chemin d’accès **site** > **wwwroot**. Faites défiler la page jusqu’à révéler le fichier *web.config* en bas de la liste.
1. Cliquez sur l’icône en forme de crayon à côté du fichier *web.config*.
1. Définissez **stdoutLogEnabled** sur `true` et remplacez le chemin **stdoutLogFile** par : `\\?\%home%\LogFiles\stdout`.
1. Sélectionnez **Enregistrer** pour enregistrer le fichier *web.config* mis à jour.
1. Adressez une requête à l’application.
1. Revenez au portail Azure. Sélectionnez le panneau **Outils avancés** dans la zone **OUTILS DE DÉVELOPPEMENT**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
1. Sélectionnez le dossier **LogFiles**.
1. Inspectez la colonne **Modifié** et sélectionnez l’icône en forme de crayon pour modifier le journal stdout avec la date de dernière modification.
1. Lorsque le fichier journal s’ouvre, l’erreur s’affiche.

Désactivez la journalisation stdout, une fois les problèmes résolus :

1. Dans la **Console de diagnostic** Kudu, revenez au chemin d’accès **site** > **wwwroot** pour faire apparaître le fichier *web.config*. Ouvrez à nouveau le fichier **web.config** en sélectionnant l’icône en forme de crayon.
1. Définissez **stdoutLogEnabled** sur `false`.
1. Sélectionnez **Enregistrer** pour enregistrer le fichier.

Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Si vous ne désactivez pas le journal stdout, l’application ou le serveur risque d’échouer. Il n’existe aucune limite quant à la taille du fichier journal ou au nombre de fichiers journaux créés. N’utilisez la journalisation stdout que pour résoudre les problèmes de démarrage de l’application.
>
> Pour la journalisation générale dans une application ASP.NET Core après le démarrage, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="slow-or-hanging-app-azure-app-service"></a>Application lente ou suspendue (Azure App Service)

Si une application répond lentement ou se bloque sur une requête, voir les articles suivants :

* [Résoudre les problèmes de baisse de performances d’une application web dans Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Utiliser l’extension de site Outil de diagnostic des incidents pour capturer une image mémoire des problèmes d’exception intermittente ou de performances sur Azure Web App](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>Panneaux Monitoring

Les panneaux Monitoring offrent une expérience de résolution des erreurs différente des méthodes décrites précédemment dans la rubrique. Ils peuvent servir à diagnostiquer les erreurs de la série 500.

Vérifiez que les extensions ASP.NET Core sont installées. Si ce n’est pas le cas, installez-les manuellement :

1. Dans la section du panneau **OUTILS DE DÉVELOPPEMENT**, sélectionnez le panneau **Extensions**.
1. Les **Extensions ASP.NET Core** devraient apparaître dans la liste.
1. Si les extensions ne sont pas installées, sélectionnez le bouton **Ajouter**.
1. Choisissez les **Extensions ASP.NET Core** dans la liste.
1. Sélectionnez **OK** pour accepter les conditions légales.
1. Sélectionnez **OK** sur le panneau **Ajouter une extension**.
1. Un message contextuel d’information indique que les extensions sont bien installées.

Si la journalisation stdout n’est pas activée, suivez les étapes ci-dessous :

1. Sur le Portail Azure, sélectionnez le panneau **Outils avancés** dans la zone **OUTILS DE DÉVELOPPEMENT**. Sélectionnez le **bouton &rarr; OK** . La console Kudu s’ouvre dans un nouvel onglet ou une nouvelle fenêtre du navigateur.
1. Dans la barre de navigation en haut de la page, ouvrez **Console de débogage** et sélectionnez **CMD**.
1. Ouvrez les dossiers sur le chemin d’accès **site** > **wwwroot** et faites défiler la page jusqu’à révéler le fichier *web.config* en bas de la liste.
1. Cliquez sur l’icône en forme de crayon à côté du fichier *web.config*.
1. Définissez **stdoutLogEnabled** sur `true` et remplacez le chemin **stdoutLogFile** par : `\\?\%home%\LogFiles\stdout`.
1. Sélectionnez **Enregistrer** pour enregistrer le fichier *web.config* mis à jour.

Maintenant, activez la journalisation des diagnostics :

1. Sur le Portail Azure, sélectionnez le panneau **Journaux de diagnostic**.
1. Basculez **Journalisation des applications (système de fichiers)** et **Messages d’erreur détaillés** sur **Activé**. Sélectionnez le bouton **Enregistrer** en haut du panneau.
1. Pour inclure le suivi des demandes ayant échoué, également appelé Mise en mémoire tampon des événements d’échec des demandes (FREB), basculez **Suivi des demandes ayant échoué** sur **Activé**.
1. Sélectionnez le panneau **Flux du journal** situé juste sous le panneau **Journaux de diagnostic** sur le portail.
1. Adressez une requête à l’application.
1. La cause de l’erreur est indiquée dans les données de flux de journal.

Veillez à désactiver la journalisation stdout une fois la résolution des problèmes effectuée.

Pour afficher les journaux de suivi des demandes ayant échoué (journaux FREB) :

1. Accédez au panneau **Diagnostiquer et résoudre les problèmes** du Portail Azure.
1. Sélectionnez **Journaux de suivi des demandes ayant échoué** dans la zone **OUTILS DE PRISE EN CHARGE** de la barre latérale.

Voir la [section Traces des demandes ayant échoué de la rubrique Activer la journalisation des diagnostics pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) et [FAQ sur les performances des applications web dans Azure : comment activer le suivi des demandes ayant échoué ?](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) pour plus d’informations.

Pour plus d’informations, voir [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log).

> [!WARNING]
> Si vous ne désactivez pas le journal stdout, l’application ou le serveur risque d’échouer. Il n’existe aucune limite quant à la taille du fichier journal ou au nombre de fichiers journaux créés.
>
> Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

## <a name="troubleshoot-on-iis"></a>Résoudre les problèmes sur IIS

### <a name="application-event-log-iis"></a>Journal des événements d’application (IIS)

Accédez au Journal des événements de l’application :

1. Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .
1. Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.
1. Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.
1. Recherchez les erreurs liées à l’application défectueuse. Les erreurs sont signalées par une valeur *IIS AspNetCore Module* (Module AspNetCore IIS) ou *IIS Express AspNetCore Module* (Module AspNetCore IIS Express) dans la colonne *Source*.

### <a name="run-the-app-at-a-command-prompt"></a>Exécuter l’application depuis une invite de commandes

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans le Journal des événements de l’application. Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte.

#### <a name="framework-dependent-deployment"></a>Déploiement dépendant du framework

Si l’application est un [déploiement dépendant du framework](/dotnet/core/deploying/#framework-dependent-deployments-fdd) :

1. Depuis une invite de commandes, accédez au dossier de déploiement et exécutez l’application en exécutant l’assembly de l’application avec *dotnet.exe*. Dans la commande suivante, remplacez le nom de l’assembly de l’application par \<assembly_name> : `dotnet .\<assembly_name>.dll` .
1. La sortie de console de l’application est écrite dans la fenêtre de console, affichant toutes les erreurs éventuelles.
1. Si les erreurs se produisent pendant qu’une requête est adressée à l’application, effectuez une requête en direction de l’hôte et du port sur lequel Kestrel écoute. À l’aide de l’hôte et du port par défaut, faites une requête en direction de `http://localhost:5000/`. Si l’application répond normalement à l’adresse de point de terminaison Kestrel, le problème est probablement lié à la configuration de l’hébergement plutôt qu’à l’application.

#### <a name="self-contained-deployment"></a>Déploiement autonome

Si l’application est un [déploiement autonome](/dotnet/core/deploying/#self-contained-deployments-scd) :

1. Depuis une invite de commandes, accédez au dossier de déploiement et exécutez l’exécutable de l’application. Dans la commande suivante, remplacez le nom de l’assembly de l’application par \<assembly_name> : `<assembly_name>.exe` .
1. La sortie de console de l’application est écrite dans la fenêtre de console, affichant toutes les erreurs éventuelles.
1. Si les erreurs se produisent pendant qu’une requête est adressée à l’application, effectuez une requête en direction de l’hôte et du port sur lequel Kestrel écoute. À l’aide de l’hôte et du port par défaut, faites une requête en direction de `http://localhost:5000/`. Si l’application répond normalement à l’adresse de point de terminaison Kestrel, le problème est probablement lié à la configuration de l’hébergement plutôt qu’à l’application.

### <a name="aspnet-core-module-stdout-log-iis"></a>Journal stdout du module ASP.NET Core (IIS)

Pour activer et afficher les journaux stdout :

1. Accédez au dossier de déploiement du site sur le système hôte.
1. Si le dossier *logs* n’est pas présent, créez-le. Pour obtenir des instructions sur l’activation de MSBuild et créer le dossier *logs* dans le déploiement automatiquement, consultez la rubrique [Structure des répertoires](xref:host-and-deploy/directory-structure).
1. Modifiez le fichier *web.config*. Définissez **stdoutLogEnabled** sur `true` et faites pointer le chemin **stdoutLogFile** vers le dossier *logs* (par exemple, `.\logs\stdout`). Dans le chemin, `stdout` est le préfixe du nom du fichier journal. Un horodatage, un ID de processus et une extension de fichier sont ajoutés automatiquement quand le journal est créé. Si `stdout` est utilisé comme préfixe du nom de fichier, un fichier journal classique porte le nom *stdout_20180205184032_5412.log*.
1. Vérifiez que l’identité de votre pool d’applications dispose des autorisations d’écriture sur le dossier *logs*.
1. Enregistrez le fichier *web.config* mis à jour.
1. Adressez une requête à l’application.
1. Accédez au dossier *logs*. Recherchez et ouvrez le journal stdout le plus récent.
1. Déterminez si le journal contient des erreurs.

Désactivez la journalisation stdout, une fois les problèmes résolus :

1. Modifiez le fichier *web.config*.
1. Définissez **stdoutLogEnabled** sur `false`.
1. Enregistrez le fichier .

Pour plus d’informations, consultez <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Si vous ne désactivez pas le journal stdout, l’application ou le serveur risque d’échouer. Il n’existe aucune limite quant à la taille du fichier journal ou au nombre de fichiers journaux créés.
>
> Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

### <a name="enable-the-developer-exception-page"></a>Afficher la page d’exception de développeur

Vous pouvez ajouter la  [variable d’environnement `ASPNETCORE_ENVIRONMENT` au fichier web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) pour exécuter l’application dans l’environnement de développement. Tant que l’environnement n’est pas substitué dans le démarrage de l’application par `UseEnvironment` sur le générateur de l’hôte, la définition de la variable d’environnement permet à la [page d’exception de développeur](xref:fundamentals/error-handling) d’apparaître quand l’application est exécutée.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

La définition de la variable d’environnement `ASPNETCORE_ENVIRONMENT` est recommandée uniquement en vue d’une utilisation sur les serveurs de préproduction et de test qui ne sont pas exposés à Internet. Supprimez la variable d’environnement du fichier *web.config* une fois la résolution des problèmes effectuée. Pour plus d’informations sur la définition des variables d’environnement dans *web.config*, consultez la section sur [l’élément enfant environmentVariables d’aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).

### <a name="obtain-data-from-an-app"></a>Obtenir des données à partir d’une application

Si une application est capable de répondre aux requêtes, obtenez des informations sur une requête, une connexion et d’autres informations supplémentaires à partir d’une application à l’aide de l’intergiciel en ligne terminal. Pour obtenir des informations supplémentaires ainsi qu'un code d'exemple, consultez <xref:test/troubleshoot#obtain-data-from-an-app>.

### <a name="slow-or-hanging-app-iis"></a>Application lente ou bloquée (IIS)

Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.

#### <a name="app-crashes-or-encounters-an-exception"></a>L’application cesse de fonctionner ou rencontre une exception

Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :

1. Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`. Le pool d’application doit disposer des accès d’écriture dans le dossier.
1. Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1) :
   * Si l’application utilise le [modèle d’hébergement in-process](xref:host-and-deploy/iis/index#in-process-hosting-model), exécutez le script pour *w3wp.exe* :

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Si l’application utilise le [modèle d’hébergement out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), exécutez le script pour *dotnet.exe* :

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.
1. Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1) :
   * Si l’application utilise le [modèle d’hébergement in-process](xref:host-and-deploy/iis/index#in-process-hosting-model), exécutez le script pour *w3wp.exe* :

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Si l’application utilise le [modèle d’hébergement out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), exécutez le script pour *dotnet.exe* :

     ```console
     .\DisableDumps dotnet.exe
     ```

Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement. Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.

> [!WARNING]
> Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>L’application se bloque, ne démarre pas ou s’exécute normalement

Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.

#### <a name="analyze-the-dump"></a>Analyser le fichier dump

Un fichier dump peut être analysé à l’aide de plusieurs approches. Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).

## <a name="clear-package-caches"></a>Effacer les caches de package

Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :

1. Supprimez les dossiers *bin* et *obj*.
1. Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.

   L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` . *NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).

1. Restaurez et regénérez le projet.
1. Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Documentation Azure

* [Application Insights pour ASP.NET Core](/azure/application-insights/app-insights-asp-net-core)
* [Section débogage à distance des applications Web de dépanner une application Web dans Azure App Service à l’aide de Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Présentation des diagnostics Azure App Service](/azure/app-service/app-service-diagnostics)
* [Surveillance des applications dans Azure App Service](/azure/app-service/web-sites-monitor)
* [Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Résoudre les erreurs HTTP de type « 502 Passerelle incorrecte » et « 503 Service indisponible » dans vos applications web Azure](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Résoudre les problèmes de baisse de performances d’une application web dans Azure App Service](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [FAQ sur les performances des applications web Azure dans Azure](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Bac à sable des applications web Azure (limitations de l’exécution du runtime App Service)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday: Azure App Service Diagnostic and Troubleshooting Experience (12-minute video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Documentation de Visual Studio

* [Débogage à distance ASP.NET Core sur IIS dans Azure dans Visual Studio 2017](/visualstudio/debugger/remote-debugging-azure)
* [Débogage à distance ASP.NET Core sur un ordinateur IIS distant dans Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Apprenez à déboguer à l’aide de Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Documentation de Visual Studio Code

* [Débogage avec Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
