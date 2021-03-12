---
title: Module ASP.NET Core
author: rick-anderson
description: En savoir plus sur le module ASP.NET Core pour l’hébergement d’applications ASP.NET Core avec IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: ffa2a7c73e00ccfb5dd726a084352c0b80cc41b0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586967"
---
# <a name="aspnet-core-module"></a>Module ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti)et [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-5.0"

Le module ASP.NET Core est un module IIS natif qui se connecte au pipeline IIS, ce qui permet aux applications ASP.NET Core de fonctionner avec IIS. Exécutez ASP.NET Core des applications avec IIS en procédant de l’une des deux : 

* L’hébergement d’une application ASP.NET Core à l’intérieur du processus de travail IIS ( `w3wp.exe` ), appelé [modèle d’hébergement in-process](xref:host-and-deploy/iis/in-process-hosting).
* Transfert des requêtes Web à un serveur principal ASP.NET Core application exécutant le serveur Kestrel, appelé [modèle d’hébergement hors processus](xref:host-and-deploy/iis/out-of-process-hosting).

Il existe des compromis entre chacun des modèles d’hébergement. Par défaut, le modèle d’hébergement in-process est utilisé en raison de meilleures performances et Diagnostics.

## <a name="install-aspnet-core-module"></a>Installer le module ASP.NET Core

Téléchargez le programme d’installation à l’aide du lien suivant :

[Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

Pour plus d’informations sur l’installation du module ASP.NET Core ou sur l’installation de différentes versions du module, consultez [installer le bundle d’hébergement .net Core](xref:host-and-deploy/iis/hosting-bundle).

Pour une expérience de tutoriel sur la publication d'une app ASP.NET Core sur un serveur IIS, voir <xref:tutorials/publish-to-iis>.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Le module ASP.NET Core est un module IIS natif qui se connecte dans le pipeline IIS pour effectuer l’une des opérations suivantes :

* Héberger une application ASP.NET Core à l’intérieur du processus de travail IIS (`w3wp.exe`), appelé [modèle d’hébergement in-process](#in-process-hosting-model).
* Transférer les requêtes web à une application ASP.NET Core du back-end exécutant le [serveur Kestrel](xref:fundamentals/servers/kestrel), appelé [modèle d’hébergement out-of-process](#out-of-process-hosting-model).

Versions de Windows prises en charge :

* Windows 7 ou version ultérieure
* Windows Server 2012 R2 ou version ultérieure

Lors de l’hébergement in-process, le module utilise une implémentation du serveur in-process pour IIS, nommée serveur HTTP IIS (`IISHttpServer`).

Lors de l’hébergement out-of-process, le module fonctionne uniquement avec Kestrel. Le module ne fonctionne pas avec [HTTP.sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modèles d'hébergement

### <a name="in-process-hosting-model"></a>Modèle d’hébergement in-process

ASP.NET Core les applications par défaut au modèle d’hébergement in-process.

Les caractéristiques suivantes s’appliquent lors de l’hébergement in-process :

* Le serveur HTTP IIS (`IISHttpServer`) est utilisé à la place du serveur [Kestrel](xref:fundamentals/servers/kestrel). Pour in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> pour :

  * Enregistrer `IISHttpServer`.
  * Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.
  * Configurer l’hôte pour capturer des erreurs de démarrage.

* L’[attribut requestTimeout](#attributes-of-the-aspnetcore-element) ne s’applique pas à l’hébergement in-process.

* Le partage d’un pool d’applications entre les applications n’est pas pris en charge. Utilisez un pool d’applications par application.

* Lorsque vous utilisez [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou que vous placez manuellement un [ `app_offline.htm` fichier dans le déploiement](xref:host-and-deploy/iis/index#locked-deployment-files), l’application risque de ne pas s’arrêter immédiatement s’il existe une connexion ouverte. Par exemple, une connexion WebSocket peut retarder l’arrêt de l’application.

* L’architecture (nombre de bits) de l’application et le runtime installé (x64 ou x86) doivent correspondre à l’architecture du pool d’applications.

* Les déconnexions du client sont détectées. Le [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) jeton d’annulation est annulé lorsque le client se déconnecte.

* Dans ASP.NET Core 2.2.1 ou version antérieure, <xref:System.IO.Directory.GetCurrentDirectory*> retourne le répertoire Worker du processus démarré par IIS plutôt que le répertoire de l’application (par exemple, `C:\Windows\System32\inetsrv` pour `w3wp.exe` ).

  Pour obtenir un exemple de code qui définit le répertoire actif de l’application, consultez la [ `CurrentDirectoryHelpers` classe](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs). Appelez la méthode `SetCurrentDirectory` . Les appels suivants à <xref:System.IO.Directory.GetCurrentDirectory*> fournissent le répertoire de l’application.

* Dans le cas d’un hébergement in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> n’est pas appelé en interne pour initialiser un utilisateur. Par conséquent, une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> utilisée pour transformer les revendications après chaque authentification n’est pas activée par défaut. Si vous transformez les revendications avec une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, appelez <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pour ajouter des services d’authentification :

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

Pour out-of-process, les [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) appels <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> à :

* Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.
* Configurer l’hôte pour capturer des erreurs de démarrage.

### <a name="hosting-model-changes"></a>Modifications du modèle d’hébergement

Si le `hostingModel` paramètre est modifié dans le `web.config` fichier (expliqué dans la section [configuration `web.config` avec](#configuration-with-webconfig) ), le module recycle le processus de travail pour IIS.

Pour IIS Express, le module ne recycle pas le processus de travail, mais déclenche plutôt un arrêt approprié du processus IIS Express en cours. La requête suivante à l’application génère un nouveau processus IIS Express.

### <a name="process-name"></a>Nom du processus

`Process.GetCurrentProcess().ProcessName` signale `w3wp`/`iisexpress` (in-process) ou `dotnet` (out-of-process).

De nombreux modules natifs, comme l’authentification Windows, restent actifs. Pour obtenir plus d’informations sur les modules IIS actifs avec le module ASP.NET Core, consultez <xref:host-and-deploy/iis/modules>.

Le module ASP.NET Core peut également  :

* Définir des variables d’environnement pour le processus de travail.
* Journaliser la sortie de stdout dans un stockage de fichiers pour résoudre les problèmes de démarrage.
* Transférer des jetons d’authentification Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Comment installer et utiliser le module ASP.NET Core

Pour obtenir des instructions sur l’installation du module ASP.NET Core, consultez [Installer le bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Configuration avec web.config

Le module ASP.NET Core est configuré avec la section `aspNetCore` du nœud `system.webServer` dans le fichier *web.config* du site.

Le `web.config` fichier suivant est publié pour un [déploiement dépendant du Framework](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) et configure le module ASP.net Core pour gérer les demandes de site :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Le fichier *web.config* suivant est publié pour un [déploiement autonome](/dotnet/articles/core/deploying/#self-contained-deployments-scd) :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

La <xref:System.Configuration.SectionInformation.InheritInChildApplications*> propriété a la valeur `false` pour indiquer que les paramètres spécifiés dans l' [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) élément ne sont pas hérités par les applications qui résident dans un sous-répertoire de l’application.

Lorsqu’une application est déployée sur [Azure App Service](https://azure.microsoft.com/services/app-service/), le chemin d’accès `stdoutLogFile` est défini sur `\\?\%home%\LogFiles\stdout`. Le chemin d’accès enregistre les journaux stdout dans le `LogFiles` dossier, qui est un emplacement créé automatiquement par le service.

Pour plus d’informations sur la configuration d’une sous-application IIS, consultez <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Attributs de l’élément aspNetCore

| Attribut | Description | Default |
| --------- | ----------- | :-----: |
| `arguments` | <p>Attribut de chaîne facultatif.</p><p>Arguments pour l’exécutable spécifié dans **processPath**.</p> | |
| `disableStartUpErrorPage` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, la page **502.5 - Échec du processus** est supprimée, et la page de code d’état 502 configurée dans le fichier *web.config* est prioritaire.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, le jeton est transmis au processus enfant qui écoute `%ASPNETCORE_PORT%` en tant qu’en-tête `'MS-ASPNETCORE-WINAUTHTOKEN'` par demande. Il incombe à ce processus d’appeler CloseHandle sur ce jeton par demande.</p> | `true` |
| `hostingModel` | <p>Attribut de chaîne facultatif.</p><p>Spécifie le modèle d’hébergement comme in-process ( `InProcess` / `inprocess` ) ou out-of-process ( `OutOfProcess` / `outofprocess` ).</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Attribut entier facultatif.</p><p>Spécifie le nombre d’instances du processus indiqué dans le paramètre **processPath** qui peuvent être lancées par application.</p><p>&dagger;Pour l’hébergement in-process, la valeur est limitée à `1`.</p><p>Il est déconseillé de définir `processesPerApplication`. Cet attribut sera supprimé dans une version ultérieure.</p> | Valeur par défaut : `1`<br>Min : `1`<br>Max : `100`&dagger; |
| `processPath` | <p>Attribut de chaîne requis.</p><p>Chemin d’accès au fichier exécutable lançant un processus d’écoute des requêtes HTTP. Les chemins d’accès relatifs sont pris en charge. Si le chemin d’accès commence par `.`, il est considéré comme étant relatif par rapport à la racine du site.</p> | |
| `rapidFailsPerMinute` | <p>Attribut entier facultatif.</p><p>Indique le nombre de fois où le processus spécifié dans **processPath** est autorisé à se bloquer par minute. Si cette limite est dépassée, le module arrête le lancement du processus pour le reste de la minute.</p><p>Non pris en charge avec hébergement in-process.</p> | Valeur par défaut : `10`<br>Min : `0`<br>Max : `100` |
| `requestTimeout` | <p>Attribut timespan facultatif.</p><p>Spécifie la durée pendant laquelle le module ASP.NET Core attend une réponse de la part du processus à l’écoute sur %ASPNETCORE_PORT%.</p><p>Dans les versions du module ASP.NET Core fournies avec ASP.NET Core 2.1 ou version ultérieure, la valeur `requestTimeout` est spécifiée en heures, minutes et secondes.</p><p>Ne s’applique pas à l’hébergement in-process. Pour l’hébergement in-process, le module attend que l’application traite la requête.</p><p>Les valeurs valides pour les segments de minutes et secondes de la chaîne sont dans la plage de 0 à 59. L’utilisation de **60** dans la valeur de minutes ou de secondes affiche *500 - Erreur interne du serveur*.</p> | Valeur par défaut : `00:02:00`<br>Min : `00:00:00`<br>Max : `360:00:00` |
| `shutdownTimeLimit` | <p>Attribut entier facultatif.</p><p>Durée en secondes pendant laquelle le module attend que l’exécutable s’arrête normalement lorsque le fichier *app_offline.htm* est détecté.</p> | Valeur par défaut : `10`<br>Min : `0`<br>Max : `600` |
| `startupTimeLimit` | <p>Attribut entier facultatif.</p><p>Durée en secondes pendant laquelle le module attend que le fichier exécutable démarre un processus à l’écoute sur le port. Si cette limite de temps est dépassée, le module met fin au processus.</p><p>Lors de l’hébergement *in-process*: le processus n’est **pas** redémarré et n’utilise **pas** le paramètre **rapidFailsPerMinute** .</p><p>Lors de l’hébergement *out-of-process*: le module tente de relancer le processus lorsqu’il reçoit une nouvelle demande et continue de tenter de redémarrer le processus lors des demandes entrantes suivantes, sauf si l’application ne parvient pas à démarrer **rapidFailsPerMinute** nombre de fois au cours de la dernière minute.</p><p>La valeur 0 (zéro) n’est **pas** considérée comme un délai infini.</p> | Valeur par défaut : `120`<br>Min : `0`<br>Max : `3600` |
| `stdoutLogEnabled` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, les attributs **stdout** et **stderr** du processus spécifié dans **processPath** sont redirigés vers le fichier spécifié dans **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Attribut de chaîne facultatif.</p><p>Spécifie le chemin d’accès relatif ou absolu pour lequel les attributs **stdout** et **stderr** du processus spécifié dans **processPath** sont consignés. Les chemins d’accès relatifs sont relatifs par rapport à la racine du site. Tout chemin d’accès commençant par `.` est relatif par rapport à la racine du site et tous les autres chemins d’accès sont traités comme des chemins d’accès absolus. Tous les dossiers fournis dans le chemin sont créés par le module au moment de la création du fichier journal. À l’aide de délimiteurs de trait de soulignement, un horodateur, un ID de processus et une extension de fichier ( `.log` ) sont ajoutés au dernier segment du chemin d’accès **stdoutLogFile** . Si `.\logs\stdout` est fourni en tant que valeur, un exemple de journal stdout est enregistré en tant que `stdout_20180205194132_1934.log` dans le `logs` dossier lorsqu’il est enregistré sur 2/5/2018 à 19:41:32 avec l’ID de processus 1934.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Définir des variables d’environnement

Des variables d’environnement peuvent être spécifiées pour le processus dans l’attribut `processPath`. Spécifiez une variable d’environnement à l’aide de l’élément enfant `<environmentVariable>` d’un élément de collection `<environmentVariables>`. Les variables d’environnement définies dans cette section prévalent sur les variables d’environnement système.

L’exemple suivant définit deux variables d’environnement dans `web.config` . `ASPNETCORE_ENVIRONMENT` définit l’environnement de l’application sur `Development`. Un développeur peut définir temporairement cette valeur dans le `web.config` fichier afin de forcer le chargement de la [page d’exception du développeur](xref:fundamentals/error-handling) lors du débogage d’une exception d’application. `CONFIG_DIR` est un exemple de variable d’environnement définie par l’utilisateur, dans laquelle le développeur a écrit du code qui lit la valeur de démarrage afin de former un chemin d’accès pour le chargement du fichier de configuration de l’application.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Une alternative à la définition de l’environnement directement dans `web.config` consiste à inclure la `<EnvironmentName>` propriété dans le [profil de publication ( `.pubxml` )](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier projet. Cette approche définit l’environnement dans le `web.config` moment où le projet est publié :
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Affectez uniquement `Development` à la variable d’environnement `ASPNETCORE_ENVIRONMENT` sur les serveurs de test et de préproduction non accessibles aux réseaux non approuvés, par exemple Internet.

## `app_offline.htm`

Si un fichier portant le nom `app_offline.htm` est détecté dans le répertoire racine d’une application, le Module ASP.net Core tente d’arrêter correctement l’application et d’arrêter le traitement des demandes entrantes. Si l’application est toujours active après le nombre de secondes défini dans `shutdownTimeLimit`, le module ASP.NET Core met fin au processus en cours d’exécution.

Pendant que le `app_offline.htm` fichier est présent, le Module ASP.net Core répond aux demandes en renvoyant le contenu du `app_offline.htm` fichier. Lorsque le `app_offline.htm` fichier est supprimé, la requête suivante démarre l’application.

Lorsque vous utilisez le modèle d’hébergement out-of-process, l’application peut ne pas s’arrêter immédiatement s’il existe une connexion ouverte. Par exemple, une connexion WebSocket peut retarder l’arrêt de l’application.

## <a name="start-up-error-page"></a>Page d’erreur de démarrage

Les hébergements in-process et out-of-process produisent des pages d’erreurs personnalisées quand ils ne parviennent pas à démarrer l’application.

Si le module ASP.NET Core ne trouve pas le gestionnaire de requêtes in-process ou out-of-process, une page de code d’état *500.0 - Échec de chargement du gestionnaire in-process/out-of-process* s’affiche.

Pour l’hébergement in-process, si le module ASP.NET Core ne peut pas démarrer l’application, une page de code d’état *500.30 - Échec de démarrage* s’affiche.

Pour l’hébergement out-of-process, si le module ASP.NET Core ne peut pas lancer le processus backend ou que ce dernier démarre mais ne parvient pas à écouter sur le port configuré, une page de code d’état *502.5 - Échec du processus* s’affiche.

Pour supprimer cette page et revenir à la page IIS de codes d’état 5xx par défaut, utilisez l’attribut `disableStartUpErrorPage`. Pour plus d’informations sur la configuration des messages d’erreur personnalisés, consultez [Erreurs HTTP `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Création et redirection de journal

Le module ASP.NET Core redirige la sortie de console stdout et stderr vers le disque si les attributs `stdoutLogEnabled` et `stdoutLogFile` de l’élément `aspNetCore` sont définis. Tous les dossiers du chemin `stdoutLogFile` sont créés par le module au moment de la création du fichier journal. Le pool d’applications doit avoir un accès en écriture à l’emplacement auquel les journaux sont écrits (utilisez `IIS AppPool\<app_pool_name>` pour fournir les autorisations d’écriture).

Aucune rotation n’est appliquée aux journaux, sauf en cas de recyclage/redémarrage du processus. Il incombe à l’hébergeur de limiter l’espace disque utilisé par les journaux.

L’utilisation du journal stdout est recommandée uniquement pour résoudre les problèmes de démarrage d’application lors de l’hébergement sur IIS ou lors de l’utilisation [de la prise en charge au moment du développement pour IIS avec Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), et non pendant le débogage local et l’exécution de l’application avec IIS Express.

N’utilisez pas le journal stdout à des fins de journalisation d’application générale. Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

Un horodatage et une extension de fichier sont ajoutés automatiquement quand le fichier journal est créé. Le nom du fichier journal est composé en ajoutant l’horodateur, l’ID de processus et l’extension de fichier ( `.log` ) au dernier segment du `stdoutLogFile` chemin (généralement `stdout` ) délimité par des traits de soulignement. Si le `stdoutLogFile` chemin d’accès se termine par `stdout` , un journal pour une application avec un PID de 1934 créé le 2/5/2018 à 19:42:32 a le nom de fichier `stdout_20180205194132_1934.log` .

Si `stdoutLogEnabled` a la valeur false, les erreurs qui se produisent au moment du démarrage de l’application sont capturées et émises dans le journal des événements (30 Ko maximum). Après le démarrage, tous les journaux supplémentaires sont ignorés.

L’exemple `aspNetCore` d’élément suivant configure la journalisation stdout sur le chemin d’accès relatif `.\log\` . Vérifiez que l’identité de l’utilisateur AppPool à l’autorisation d’écrire dans le chemin d’accès fourni.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Lors de la publication d’une application pour le déploiement Azure App Service, le kit de développement logiciel (SDK) Web définit la `stdoutLogFile` valeur sur `\\?\%home%\LogFiles\stdout` . La `%home` variable d’environnement est prédéfinie pour les applications hébergées par Azure App service.

Pour créer des règles de filtre de journalisation, consultez les sections [configuration](xref:fundamentals/logging/index#log-filtering) et [filtrage des journaux](xref:fundamentals/logging/index#log-filtering) de la documentation ASP.net Core Logging.

Pour plus d’informations sur les formats de chemin d’accès, consultez [formats de chemin d’accès de fichier sur les systèmes Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Journaux de diagnostic améliorés

Le module ASP.NET Core est configurable pour proposer des journaux de diagnostic améliorés. Ajoutez l' `<handlerSettings>` élément à l' `<aspNetCore>` élément dans `web.config` . L’affectation de la valeur `TRACE` à `debugLevel` expose une fidélité plus élevée des informations de diagnostic :

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Tous les dossiers dans le chemin d’accès ( `logs` dans l’exemple précédent) sont créés par le module lors de la création du fichier journal. Le pool d’applications doit avoir un accès en écriture à l’emplacement où les journaux sont écrits (utilisez `IIS AppPool\{APP POOL NAME}` , où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications, pour fournir l’autorisation d’écriture).

Les valeurs du niveau de débogage (`debugLevel`) peuvent inclure à la fois le niveau et l’emplacement.

Niveaux (dans l’ordre du moins au plus détaillé) :

* ERROR
* WARNING
* INFO
* TRACE

Emplacements (plusieurs emplacements sont autorisés) :

* CONSOLE
* EVENTLOG
* FILE

Les paramètres de gestionnaire peuvent également être fournis par le biais de variables d’environnement :

* `ASPNETCORE_MODULE_DEBUG_FILE`: Chemin d’accès au fichier journal de débogage. (Par défaut : `aspnetcore-debug.log` )
* `ASPNETCORE_MODULE_DEBUG`: Paramètre de niveau de débogage.

> [!WARNING]
> Ne laissez **pas** la journalisation du débogage activée dans le déploiement plus longtemps que nécessaire pour résoudre un problème. La taille du journal n’est pas limitée. Si vous laissez la journalisation du débogage activée, vous risquez d’épuiser l’espace disque disponible et de bloquer le serveur ou le service d’application.

Pour obtenir un exemple de l’élément dans le fichier, consultez [configuration avec web.config](#configuration-with-webconfig) `aspNetCore` `web.config` .

## <a name="modify-the-stack-size"></a>Modifier la taille de pile

*S’applique uniquement lors de l’utilisation du modèle d’hébergement in-process.*

Configurez la taille de la pile managée en utilisant le `stackSize` paramètre en octets dans `web.config` . La taille par défaut est de 1 048 576 octets (1 Mo).

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

Le programme d’installation du module ASP.NET Core s’exécute avec les privilèges du compte **TrustedInstaller**. Étant donné que le compte système local ne dispose pas de l’autorisation de modification pour le chemin d’accès de partage utilisé par la configuration partagée IIS, le programme d’installation lève une erreur d’accès refusé lors de la tentative de configuration des paramètres du module dans le `applicationHost.config` fichier sur le partage.

Quand une configuration partagée IIS est utilisée sur la même machine que l’installation IIS, il convient d’exécuter le programme d’installation du bundle d’hébergement ASP.NET Core avec le paramètre `OPT_NO_SHARED_CONFIG_CHECK` défini sur `1` :

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Quand le chemin de la configuration partagée ne se trouve pas sur la même machine que l’installation IIS, effectuez ces étapes :

1. Désactivez la configuration partagée IIS.
1. Exécutez le programme d’installation.
1. Exportez le fichier mis à jour `applicationHost.config` vers le partage.
1. Réactivez la configuration partagée IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Version du module et journaux du programme d’installation du bundle d’hébergement

Pour déterminer la version du module ASP.NET Core installé :

1. Sur le système d’hébergement, accédez à `%windir%\System32\inetsrv` .
1. Localisez le `aspnetcore.dll` fichier.
1. Cliquez avec le bouton droit sur le fichier, puis sélectionnez **Propriétés** dans le menu contextuel.
1. Sélectionnez l’onglet **Détails** . La version du **fichier** et la version du **produit** représentent la version installée du module.

Les journaux du programme d’installation du bundle d’hébergement pour le module sont disponibles à l’adresse `C:\Users\%UserName%\AppData\Local\Temp` . Le fichier est nommé `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.

## <a name="module-schema-and-configuration-file-locations"></a>Emplacements des fichiers du module, du schéma et de configuration

### <a name="module"></a>Module

**IIS (x86/amd64) :**

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/amd64) :**

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

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Le module ASP.NET Core est un module IIS natif qui se connecte dans le pipeline IIS pour effectuer l’une des opérations suivantes :

* Héberger une application ASP.NET Core à l’intérieur du processus de travail IIS (`w3wp.exe`), appelé [modèle d’hébergement in-process](#in-process-hosting-model).
* Transférer les requêtes web à une application ASP.NET Core du back-end exécutant le [serveur Kestrel](xref:fundamentals/servers/kestrel), appelé [modèle d’hébergement out-of-process](#out-of-process-hosting-model).

Versions de Windows prises en charge :

* Windows 7 ou version ultérieure
* Windows Server 2008 R2 ou version ultérieure

Lors de l’hébergement in-process, le module utilise une implémentation du serveur in-process pour IIS, nommée serveur HTTP IIS (`IISHttpServer`).

Lors de l’hébergement out-of-process, le module fonctionne uniquement avec Kestrel. Le module ne fonctionne pas avec [HTTP.sys](xref:fundamentals/servers/httpsys).

## <a name="hosting-models"></a>Modèles d'hébergement

### <a name="in-process-hosting-model"></a>Modèle d’hébergement in-process

Pour configurer l’hébergement in-process dans une application, ajoutez la propriété `<AspNetCoreHostingModel>` au fichier projet de l’application en lui attribuant la valeur `InProcess` (l’hébergement out-of-process est défini sur `OutOfProcess`) :

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

Le modèle d’hébergement in-process n’est pas pris en charge pour les applications ASP.NET Core qui ciblent le .NET Framework.

La valeur de ne respecte pas la `<AspNetCoreHostingModel>` casse, donc `inprocess` et `outofprocess` sont des valeurs valides.

Si la propriété `<AspNetCoreHostingModel>` n’est pas présente dans le fichier, la valeur par défaut est `OutOfProcess`.

Les caractéristiques suivantes s’appliquent lors de l’hébergement in-process :

* Le serveur HTTP IIS (`IISHttpServer`) est utilisé à la place du serveur [Kestrel](xref:fundamentals/servers/kestrel). Pour in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> pour :

  * Enregistrer `IISHttpServer`.
  * Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.
  * Configurer l’hôte pour capturer des erreurs de démarrage.

* L’[attribut requestTimeout](#attributes-of-the-aspnetcore-element) ne s’applique pas à l’hébergement in-process.

* Le partage d’un pool d’applications entre les applications n’est pas pris en charge. Utilisez un pool d’applications par application.

* Lorsque vous utilisez [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) ou que vous placez manuellement un [fichier app_offline.htm dans le déploiement](xref:host-and-deploy/iis/index#locked-deployment-files), l’application peut ne pas s’arrêter immédiatement si une connexion est ouverte. Par exemple, une connexion websocket peut retarder l’arrêt de l’application.

* L’architecture (nombre de bits) de l’application et le runtime installé (x64 ou x86) doivent correspondre à l’architecture du pool d’applications.

* Les déconnexions du client sont détectées. Le jeton d’annulation [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) est annulé quand le client se déconnecte.

* Dans ASP.NET Core version 2.2.1 ou antérieure, <xref:System.IO.Directory.GetCurrentDirectory*> retourne le répertoire de travail du processus démarré par IIS, et non le répertoire de l’application (par exemple, *C:\Windows\System32\inetsrv* pour *w3wp.exe*).

  Pour connaître l’exemple de code qui définit le répertoire actuel de l’application, consultez [CurrentDirectoryHelpers, classe](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs). Appelez la méthode `SetCurrentDirectory` . Les appels suivants à <xref:System.IO.Directory.GetCurrentDirectory*> fournissent le répertoire de l’application.

* Dans le cas d’un hébergement in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> n’est pas appelé en interne pour initialiser un utilisateur. Par conséquent, une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> utilisée pour transformer les revendications après chaque authentification n’est pas activée par défaut. Si vous transformez les revendications avec une implémentation de <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>, appelez <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> pour ajouter des services d’authentification :

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

### <a name="out-of-process-hosting-model"></a>Modèle d’hébergement out-of-process

Pour configurer une application pour un hébergement out-of-process, utilisez l’une des approches suivantes dans le fichier de projet :

* Ne spécifiez pas la propriété `<AspNetCoreHostingModel>`. Si la propriété `<AspNetCoreHostingModel>` n’est pas présente dans le fichier, la valeur par défaut est `OutOfProcess`.
* Définissez la valeur de la propriété `<AspNetCoreHostingModel>` sur `OutOfProcess` (l’hébergement in-process est défini avec `InProcess`) :

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

La valeur ne respecte pas la casse, donc `inprocess` et `outofprocess` sont des valeurs valides.

Le serveur [Kestrel](xref:fundamentals/servers/kestrel) est utilisé à la place du serveur HTTP IIS (`IISHttpServer`).

Pour out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) appelle <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> pour :

* Configurer le port et le chemin de base sur lesquels le serveur doit écouter lors de l’exécution derrière le module ASP.NET Core.
* Configurer l’hôte pour capturer des erreurs de démarrage.

### <a name="hosting-model-changes"></a>Modifications du modèle d’hébergement

Si le paramètre `hostingModel` est modifié dans le fichier *web.config* (comme expliqué dans la section [Configuration avec web.config](#configuration-with-webconfig)), le module recycle le processus de travail pour IIS.

Pour IIS Express, le module ne recycle pas le processus de travail, mais déclenche plutôt un arrêt approprié du processus IIS Express en cours. La requête suivante à l’application génère un nouveau processus IIS Express.

### <a name="process-name"></a>Nom du processus

`Process.GetCurrentProcess().ProcessName` signale `w3wp`/`iisexpress` (in-process) ou `dotnet` (out-of-process).

De nombreux modules natifs, comme l’authentification Windows, restent actifs. Pour obtenir plus d’informations sur les modules IIS actifs avec le module ASP.NET Core, consultez <xref:host-and-deploy/iis/modules>.

Le module ASP.NET Core peut également  :

* Définir des variables d’environnement pour le processus de travail.
* Journaliser la sortie de stdout dans un stockage de fichiers pour résoudre les problèmes de démarrage.
* Transférer des jetons d’authentification Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Comment installer et utiliser le module ASP.NET Core

Pour obtenir des instructions sur l’installation du module ASP.NET Core, consultez [Installer le bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Configuration avec web.config

Le module ASP.NET Core est configuré avec la section `aspNetCore` du nœud `system.webServer` dans le fichier *web.config* du site.

Le fichier *web.config* suivant est publié pour un [déploiement dépendant du framework](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) et configure le module ASP.NET Core pour gérer les demandes du site :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Le fichier *web.config* suivant est publié pour un [déploiement autonome](/dotnet/articles/core/deploying/#self-contained-deployments-scd) :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

La <xref:System.Configuration.SectionInformation.InheritInChildApplications*> propriété a la valeur `false` pour indiquer que les paramètres spécifiés dans l' [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) élément ne sont pas hérités par les applications qui résident dans un sous-répertoire de l’application.

Lorsqu’une application est déployée sur [Azure App Service](https://azure.microsoft.com/services/app-service/), le chemin d’accès `stdoutLogFile` est défini sur `\\?\%home%\LogFiles\stdout`. Le chemin d’accès enregistre les journaux stdout dans le dossier *LogFiles*, un emplacement automatiquement créé par le service.

Pour plus d’informations sur la configuration d’une sous-application IIS, consultez <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Attributs de l’élément aspNetCore

| Attribut | Description | Default |
| --------- | ----------- | :-----: |
| `arguments` | <p>Attribut de chaîne facultatif.</p><p>Arguments de l’exécutable spécifié dans `processPath` .</p> | |
| `disableStartUpErrorPage` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, la page **502.5 - Échec du processus** est supprimée, et la page de code d’état 502 configurée dans le fichier *web.config* est prioritaire.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, le jeton est transmis au processus enfant qui écoute sur %ASPNETCORE_PORT% sous la forme d’un en-tête 'MS-ASPNETCORE-WINAUTHTOKEN' par demande. Il incombe à ce processus d’appeler CloseHandle sur ce jeton par demande.</p> | `true` |
| `hostingModel` | <p>Attribut de chaîne facultatif.</p><p>Spécifie le modèle d’hébergement comme in-process ( `InProcess` / `inprocess` ) ou out-of-process ( `OutOfProcess` / `outofprocess` ).</p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p>Attribut entier facultatif.</p><p>Spécifie le nombre d’instances du processus spécifié dans le `processPath` paramètre qui peut être lancé par application.</p><p>&dagger;Pour l’hébergement in-process, la valeur est limitée à `1`.</p><p>Il est déconseillé de définir `processesPerApplication`. Cet attribut sera supprimé dans une version ultérieure.</p> | Valeur par défaut : `1`<br>Min : `1`<br>Max : `100`&dagger; |
| `processPath` | <p>Attribut de chaîne requis.</p><p>Chemin d’accès au fichier exécutable lançant un processus d’écoute des requêtes HTTP. Les chemins d’accès relatifs sont pris en charge. Si le chemin d’accès commence par `.`, il est considéré comme étant relatif par rapport à la racine du site.</p> | |
| `rapidFailsPerMinute` | <p>Attribut entier facultatif.</p><p>Spécifie le nombre de fois où le processus spécifié dans `processPath` est autorisé à se bloquer par minute. Si cette limite est dépassée, le module arrête le lancement du processus pour le reste de la minute.</p><p>Non pris en charge avec hébergement in-process.</p> | Valeur par défaut : `10`<br>Min : `0`<br>Max : `100` |
| `requestTimeout` | <p>Attribut timespan facultatif.</p><p>Spécifie la durée pendant laquelle le module ASP.NET Core attend une réponse de la part du processus à l’écoute sur %ASPNETCORE_PORT%.</p><p>Dans les versions du module ASP.NET Core fournies avec ASP.NET Core 2.1 ou version ultérieure, la valeur `requestTimeout` est spécifiée en heures, minutes et secondes.</p><p>Ne s’applique pas à l’hébergement in-process. Pour l’hébergement in-process, le module attend que l’application traite la requête.</p><p>Les valeurs valides pour les segments de minutes et secondes de la chaîne sont dans la plage de 0 à 59. L’utilisation de **60** dans la valeur de minutes ou de secondes affiche *500 - Erreur interne du serveur*.</p> | Valeur par défaut : `00:02:00`<br>Min : `00:00:00`<br>Max : `360:00:00` |
| `shutdownTimeLimit` | <p>Attribut entier facultatif.</p><p>Durée en secondes pendant laquelle le module attend que l’exécutable s’arrête correctement lorsque le `app_offline.htm` fichier est détecté.</p> | Valeur par défaut : `10`<br>Min : `0`<br>Max : `600` |
| `startupTimeLimit` | <p>Attribut entier facultatif.</p><p>Durée en secondes pendant laquelle le module attend que le fichier exécutable démarre un processus à l’écoute sur le port. Si cette limite de temps est dépassée, le module met fin au processus.</p><p>Lors de l’hébergement *in-process*: le processus n’est **pas** redémarré et n’utilise **pas** le `rapidFailsPerMinute` paramètre.</p><p>En cas *d’hébergement out-of-process*: le module tente de relancer le processus lorsqu’il reçoit une nouvelle demande et continue de tenter de redémarrer le processus lors des demandes entrantes suivantes, à moins que l’application ne démarre pas le `rapidFailsPerMinute` nombre de fois au cours de la dernière minute.</p><p>La valeur 0 (zéro) n’est **pas** considérée comme un délai infini.</p> | Valeur par défaut : `120`<br>Min : `0`<br>Max : `3600` |
| `stdoutLogEnabled` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, **stdout** et **stderr** pour le processus spécifié dans `processPath` sont redirigés vers le fichier spécifié dans **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Attribut de chaîne facultatif.</p><p>Spécifie le chemin d’accès relatif ou absolu pour lequel `stdout` et `stderr` à partir du processus spécifié dans `processPath` sont journalisés. Les chemins d’accès relatifs sont relatifs par rapport à la racine du site. Tout chemin d’accès commençant par `.` est relatif par rapport à la racine du site et tous les autres chemins d’accès sont traités comme des chemins d’accès absolus. Tous les dossiers fournis dans le chemin sont créés par le module au moment de la création du fichier journal. À l’aide de délimiteurs de trait de soulignement, un horodateur, un ID de processus et une extension de fichier ( `.log` ) sont ajoutés au dernier segment du `stdoutLogFile` chemin d’accès. Si `.\logs\stdout` est fourni en tant que valeur, un exemple de journal stdout est enregistré en tant que `stdout_20180205194132_1934.log` dans le `logs` dossier lorsqu’il est enregistré sur 2/5/2018 à 19:41:32 avec l’ID de processus 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Définition des variables d'environnement

Des variables d’environnement peuvent être spécifiées pour le processus dans l’attribut `processPath`. Spécifiez une variable d’environnement à l’aide de l’élément enfant `<environmentVariable>` d’un élément de collection `<environmentVariables>`. Les variables d’environnement définies dans cette section prévalent sur les variables d’environnement système.

L’exemple suivant définit deux variables d'environnement. `ASPNETCORE_ENVIRONMENT` définit l’environnement de l’application sur `Development`. Un développeur peut définir temporairement cette valeur dans le `web.config` fichier afin de forcer le chargement de la [page d’exception du développeur](xref:fundamentals/error-handling) lors du débogage d’une exception d’application. `CONFIG_DIR` est un exemple de variable d’environnement définie par l’utilisateur, dans laquelle le développeur a écrit du code qui lit la valeur de démarrage afin de former un chemin d’accès pour le chargement du fichier de configuration de l’application.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Une alternative à la définition de l’environnement directement dans `web.config` consiste à inclure la `<EnvironmentName>` propriété dans le [profil de publication (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) ou le fichier projet. Cette approche définit l’environnement dans le `web.config` moment où le projet est publié :
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Affectez uniquement `Development` à la variable d’environnement `ASPNETCORE_ENVIRONMENT` sur les serveurs de test et de préproduction non accessibles aux réseaux non approuvés, par exemple Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Si un fichier portant le nom `app_offline.htm` est détecté dans le répertoire racine d’une application, le Module ASP.net Core tente d’arrêter correctement l’application et d’arrêter le traitement des demandes entrantes. Si l’application est toujours active après le nombre de secondes défini dans `shutdownTimeLimit`, le module ASP.NET Core met fin au processus en cours d’exécution.

Pendant que le `app_offline.htm` fichier est présent, le Module ASP.net Core répond aux demandes en renvoyant le contenu du `app_offline.htm` fichier. Lorsque le `app_offline.htm` fichier est supprimé, la requête suivante démarre l’application.

Lorsque vous utilisez le modèle d’hébergement out-of-process, l’application peut ne pas s’arrêter immédiatement s’il existe une connexion ouverte. Par exemple, une connexion websocket peut retarder l’arrêt de l’application.

## <a name="start-up-error-page"></a>Page d’erreur de démarrage

Les hébergements in-process et out-of-process produisent des pages d’erreurs personnalisées quand ils ne parviennent pas à démarrer l’application.

Si le module ASP.NET Core ne trouve pas le gestionnaire de requêtes in-process ou out-of-process, une page de code d’état *500.0 - Échec de chargement du gestionnaire in-process/out-of-process* s’affiche.

Pour l’hébergement in-process, si le module ASP.NET Core ne peut pas démarrer l’application, une page de code d’état *500.30 - Échec de démarrage* s’affiche.

Pour l’hébergement out-of-process, si le module ASP.NET Core ne peut pas lancer le processus backend ou que ce dernier démarre mais ne parvient pas à écouter sur le port configuré, une page de code d’état *502.5 - Échec du processus* s’affiche.

Pour supprimer cette page et revenir à la page IIS de codes d’état 5xx par défaut, utilisez l’attribut `disableStartUpErrorPage`. Pour plus d’informations sur la configuration des messages d’erreur personnalisés, consultez [Erreurs HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Création et redirection de journal

Le module ASP.NET Core redirige la sortie de console stdout et stderr vers le disque si les attributs `stdoutLogEnabled` et `stdoutLogFile` de l’élément `aspNetCore` sont définis. Tous les dossiers du chemin `stdoutLogFile` sont créés par le module au moment de la création du fichier journal. Le pool d’applications doit avoir un accès en écriture à l’emplacement où les journaux sont écrits (utilisez `IIS AppPool\{APP POOL NAME}` pour fournir une autorisation d’écriture, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications).

Aucune rotation n’est appliquée aux journaux, sauf en cas de recyclage/redémarrage du processus. Il incombe à l’hébergeur de limiter l’espace disque utilisé par les journaux.

L’utilisation du journal stdout est recommandée uniquement pour résoudre les problèmes de démarrage d’application lors de l’hébergement sur IIS ou lors de l’utilisation [de la prise en charge au moment du développement pour IIS avec Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), et non pendant le débogage local et l’exécution de l’application avec IIS Express.

N’utilisez pas le journal stdout à des fins de journalisation d’application générale. Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

Un horodatage et une extension de fichier sont ajoutés automatiquement quand le fichier journal est créé. Le nom du fichier journal est composé en ajoutant l’horodateur, l’ID de processus et l’extension de fichier ( `.log` ) au dernier segment du `stdoutLogFile` chemin (généralement `stdout` ) délimité par des traits de soulignement. Si le `stdoutLogFile` chemin d’accès se termine par `stdout` , un journal pour une application avec un PID de 1934 créé le 2/5/2018 à 19:42:32 a le nom de fichier `stdout_20180205194132_1934.log` .

Si `stdoutLogEnabled` a la valeur false, les erreurs qui se produisent au moment du démarrage de l’application sont capturées et émises dans le journal des événements (30 Ko maximum). Après le démarrage, tous les journaux supplémentaires sont ignorés.

L’exemple `aspNetCore` d’élément suivant configure la journalisation stdout sur le chemin d’accès relatif `.\log\` . Vérifiez que l’identité de l’utilisateur du pool d’applications a l’autorisation d’écrire dans le chemin d’accès fourni.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Lors de la publication d’une application pour le déploiement Azure App Service, le kit de développement logiciel (SDK) Web définit la `stdoutLogFile` valeur sur `\\?\%home%\LogFiles\stdout` . La `%home` variable d’environnement est prédéfinie pour les applications hébergées par Azure App service.

Pour plus d’informations sur les formats de chemin d’accès, consultez [formats de chemin d’accès de fichier sur les systèmes Windows](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Journaux de diagnostic améliorés

Le module ASP.NET Core est configurable pour proposer des journaux de diagnostic améliorés. Ajoutez l' `<handlerSettings>` élément à l' `<aspNetCore>` élément dans `web.config` . L’affectation de la valeur `TRACE` à `debugLevel` expose une fidélité plus élevée des informations de diagnostic :

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Les dossiers dans le chemin d’accès fourni à la `<handlerSetting>` valeur ( `logs` dans l’exemple précédent) ne sont pas créés automatiquement par le module et doivent être préexistants dans le déploiement. Le pool d’applications doit avoir un accès en écriture à l’emplacement où les journaux sont écrits (utilisez `IIS AppPool\{APP POOL NAME}` pour fournir une autorisation d’écriture, où l’espace réservé `{APP POOL NAME}` est le nom du pool d’applications).

Les valeurs du niveau de débogage (`debugLevel`) peuvent inclure à la fois le niveau et l’emplacement.

Niveaux (dans l’ordre du moins au plus détaillé) :

* ERROR
* WARNING
* INFO
* TRACE

Emplacements (plusieurs emplacements sont autorisés) :

* CONSOLE
* EVENTLOG
* FILE

Les paramètres de gestionnaire peuvent également être fournis par le biais de variables d’environnement :

* `ASPNETCORE_MODULE_DEBUG_FILE`: Chemin d’accès au fichier journal de débogage. (Par défaut : `aspnetcore-debug.log` )
* `ASPNETCORE_MODULE_DEBUG`: Paramètre de niveau de débogage.

> [!WARNING]
> Ne laissez **pas** la journalisation du débogage activée dans le déploiement plus longtemps que nécessaire pour résoudre un problème. La taille du journal n’est pas limitée. Si vous laissez la journalisation du débogage activée, vous risquez d’épuiser l’espace disque disponible et de bloquer le serveur ou le service d’application.

Pour obtenir un exemple de l’élément dans le fichier, consultez [configuration avec web.config](#configuration-with-webconfig) `aspNetCore` `web.config` .

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
1. Exportez le fichier mis à jour `applicationHost.config` vers le partage.
1. Réactivez la configuration partagée IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Version du module et journaux du programme d’installation du bundle d’hébergement

Pour déterminer la version du module ASP.NET Core installé :

1. Sur le système d’hébergement, accédez à `%windir%\System32\inetsrv` .
1. Localisez le `aspnetcore.dll` fichier.
1. Cliquez avec le bouton droit sur le fichier, puis sélectionnez **Propriétés** dans le menu contextuel.
1. Sélectionnez l’onglet **Détails** . La version du **fichier** et la version du **produit** représentent la version installée du module.

Les journaux du programme d’installation du bundle d’hébergement pour le module sont disponibles à l’adresse `C:\\Users\\%UserName%\\AppData\\Local\\Temp` . Le fichier est nommé `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log` , où l’espace réservé `{TIMESTAMP}` est l’horodateur.

## <a name="module-schema-and-configuration-file-locations"></a>Emplacements des fichiers du module, du schéma et de configuration

### <a name="module"></a>Module

**IIS (x86/amd64)**:

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/amd64)**:

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

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Le module ASP.NET Core est un module IIS natif qui se connecte dans le pipeline IIS pour transférer les requêtes web aux applications ASP.NET Core du back-end.

Versions de Windows prises en charge :

* Windows 7 ou version ultérieure
* Windows Server 2008 R2 ou version ultérieure

Le module fonctionne seulement avec Kestrel. Le module n’est pas compatible avec [HTTP.sys](xref:fundamentals/servers/httpsys).

Étant donné que les applications ASP.NET Core s’exécutent dans un processus distinct du processus de travail IIS, le module traite également la gestion des processus. Le module démarre le processus de l’application ASP.NET Core quand la première requête arrive et redémarre l’application si elle plante. Il s’agit essentiellement du même comportement que celui des applications ASP.NET 4.x qui s’exécutent in-process dans IIS et sont gérées par le [service WAS (Windows Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Le schéma suivant illustre la relation entre IIS, le module ASP.NET Core et une application :

![Module ASP.NET Core](iis/index/_static/ancm-outofprocess.png)

Les requêtes arrivent du web au pilote HTTP.sys en mode noyau. Le pilote route les requêtes vers IIS sur le port configuré du site web, généralement 80 (HTTP) ou 443 (HTTPS). Le module transfère les requêtes à Kestrel sur un port aléatoire pour l’application, qui n’est ni le port 80 ni le port 443.

Le module spécifie le port via une variable d’environnement au démarrage, et l' [intergiciel (middleware) d’intégration IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configure le serveur pour l’écoute `http://localhost:{port}` . Des vérifications supplémentaires sont effectuées, et les requêtes qui ne proviennent pas du module sont rejetées. Le module ne prend pas en charge le transfert HTTPS : les requêtes sont donc transférées via HTTP, même si IIS les reçoit via HTTPS.

Dès que Kestrel sélectionne la requête dans le module, celle-ci est envoyée (push) dans le pipeline de middlewares d’ASP.NET Core. Le pipeline de middlewares traite la requête et la passe en tant qu’instance de `HttpContext` à la logique de l’application. L’intergiciel (middleware) ajouté par l’intégration d’IIS met à jour le schéma, l’adresse IP distante et la base du chemin pour prendre en compte le transfert de la requête à Kestrel. La réponse de l’application est ensuite repassée à IIS, qui la renvoie au client HTTP à l’origine de la requête.

De nombreux modules natifs, comme l’authentification Windows, restent actifs. Pour obtenir plus d’informations sur les modules IIS actifs avec le module ASP.NET Core, consultez <xref:host-and-deploy/iis/modules>.

Le module ASP.NET Core peut également  :

* Définir des variables d’environnement pour le processus de travail.
* Journaliser la sortie de stdout dans un stockage de fichiers pour résoudre les problèmes de démarrage.
* Transférer des jetons d’authentification Windows.

## <a name="how-to-install-and-use-the-aspnet-core-module"></a>Comment installer et utiliser le module ASP.NET Core

Pour obtenir des instructions sur l’installation du module ASP.NET Core, consultez [Installer le bundle d’hébergement .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

## <a name="configuration-with-webconfig"></a>Configuration avec web.config

Le module ASP.NET Core est configuré avec la section `aspNetCore` du nœud `system.webServer` dans le fichier *web.config* du site.

Le fichier *web.config* suivant est publié pour un [déploiement dépendant du framework](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) et configure le module ASP.NET Core pour gérer les demandes du site :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Le fichier *web.config* suivant est publié pour un [déploiement autonome](/dotnet/articles/core/deploying/#self-contained-deployments-scd) :

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Lorsqu’une application est déployée sur [Azure App Service](https://azure.microsoft.com/services/app-service/), le chemin d’accès `stdoutLogFile` est défini sur `\\?\%home%\LogFiles\stdout`. Le chemin d’accès enregistre les journaux stdout dans le dossier *LogFiles*, un emplacement automatiquement créé par le service.

Pour plus d’informations sur la configuration d’une sous-application IIS, consultez <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Attributs de l’élément aspNetCore

| Attribut | Description | Default |
| --------- | ----------- | :-----: |
| `arguments` | <p>Attribut de chaîne facultatif.</p><p>Arguments pour l’exécutable spécifié dans **processPath**.</p>| |
| `disableStartUpErrorPage` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, la page **502.5 - Échec du processus** est supprimée, et la page de code d’état 502 configurée dans le fichier *web.config* est prioritaire.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, le jeton est transmis au processus enfant qui écoute sur %ASPNETCORE_PORT% sous la forme d’un en-tête 'MS-ASPNETCORE-WINAUTHTOKEN' par demande. Il incombe à ce processus d’appeler CloseHandle sur ce jeton par demande.</p> | `true` |
| `processesPerApplication` | <p>Attribut entier facultatif.</p><p>Spécifie le nombre d’instances du processus indiqué dans le paramètre **processPath** qui peuvent être lancées par application.</p><p>Il est déconseillé de définir `processesPerApplication`. Cet attribut sera supprimé dans une version ultérieure.</p> | Valeur par défaut : `1`<br>Min : `1`<br>Max : `100` |
| `processPath` | <p>Attribut de chaîne requis.</p><p>Chemin d’accès au fichier exécutable lançant un processus d’écoute des requêtes HTTP. Les chemins d’accès relatifs sont pris en charge. Si le chemin d’accès commence par `.`, il est considéré comme étant relatif par rapport à la racine du site.</p> | |
| `rapidFailsPerMinute` | <p>Attribut entier facultatif.</p><p>Indique le nombre de fois où le processus spécifié dans **processPath** est autorisé à se bloquer par minute. Si cette limite est dépassée, le module arrête le lancement du processus pour le reste de la minute.</p> | Valeur par défaut : `10`<br>Min : `0`<br>Max : `100` |
| `requestTimeout` | <p>Attribut timespan facultatif.</p><p>Spécifie la durée pendant laquelle le module ASP.NET Core attend une réponse de la part du processus à l’écoute sur %ASPNETCORE_PORT%.</p><p>Dans les versions du module ASP.NET Core fournies avec ASP.NET Core 2.1 ou version ultérieure, la valeur `requestTimeout` est spécifiée en heures, minutes et secondes.</p> | Valeur par défaut : `00:02:00`<br>Min : `00:00:00`<br>Max : `360:00:00` |
| `shutdownTimeLimit` | <p>Attribut entier facultatif.</p><p>Durée en secondes pendant laquelle le module attend que l’exécutable s’arrête normalement lorsque le fichier *app_offline.htm* est détecté.</p> | Valeur par défaut : `10`<br>Min : `0`<br>Max : `600` |
| `startupTimeLimit` | <p>Attribut entier facultatif.</p><p>Durée en secondes pendant laquelle le module attend que le fichier exécutable démarre un processus à l’écoute sur le port. Si cette limite de temps est dépassée, le module met fin au processus. Le module tente de relancer le processus lorsqu’il reçoit une nouvelle requête, puis continue d’essayer de redémarrer le processus pour les requêtes entrantes suivantes, sauf si l’application ne démarre pas **rapidFailsPerMinute** un certain nombre de fois au cours de la dernière minute.</p><p>La valeur 0 (zéro) n’est **pas** considérée comme un délai infini.</p> | Valeur par défaut : `120`<br>Min : `0`<br>Max : `3600` |
| `stdoutLogEnabled` | <p>Attribut booléen facultatif.</p><p>Si la valeur est true, les attributs **stdout** et **stderr** du processus spécifié dans **processPath** sont redirigés vers le fichier spécifié dans **stdoutLogFile**.</p> | `false` |
| `stdoutLogFile` | <p>Attribut de chaîne facultatif.</p><p>Spécifie le chemin d’accès relatif ou absolu pour lequel les attributs **stdout** et **stderr** du processus spécifié dans **processPath** sont consignés. Les chemins d’accès relatifs sont relatifs par rapport à la racine du site. Tout chemin d’accès commençant par `.` est relatif par rapport à la racine du site et tous les autres chemins d’accès sont traités comme des chemins d’accès absolus. Tous les dossiers spécifiés dans le chemin d’accès doivent exister pour permettre au module de créer le fichier journal. Si vous utilisez des délimiteurs de trait de soulignement, un horodatage, un ID de processus et une extension de fichier (*.log*) sont ajoutés au dernier segment du chemin d'accès **stdoutLogFile**. Si `.\logs\stdout` est fourni en tant que valeur, un exemple de journal stdout est enregistré en tant que *stdout_20180205194132_1934.log* dans le dossier *journaux* avec un enregistrement effectué le 05/02/2018 à 19:41:32 et un ID de processus de 1934.</p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a>Définition des variables d'environnement

Des variables d’environnement peuvent être spécifiées pour le processus dans l’attribut `processPath`. Spécifiez une variable d’environnement à l’aide de l’élément enfant `<environmentVariable>` d’un élément de collection `<environmentVariables>`.

> [!WARNING]
> Les variables d’environnement définies dans cette section sont en conflit avec les variables d’environnement système qui ont le même nom. Si une variable d’environnement est définie à la fois dans le fichier *web.config* et au niveau du système dans Windows, la valeur provenant du fichier *web.config* est ajoutée à la valeur de la variable d’environnement système (par exemple, `ASPNETCORE_ENVIRONMENT: Development;Development`), ce qui empêche le démarrage de l’application.

L’exemple suivant définit deux variables d'environnement. `ASPNETCORE_ENVIRONMENT` définit l’environnement de l’application sur `Development`. Un développeur peut définir temporairement cette valeur dans le fichier *web.config* afin de forcer le chargement de la [Page d’exception de développeur](xref:fundamentals/error-handling) lors du débogage d’une exception d’application. `CONFIG_DIR` est un exemple de variable d’environnement définie par l’utilisateur, dans laquelle le développeur a écrit du code qui lit la valeur de démarrage afin de former un chemin d’accès pour le chargement du fichier de configuration de l’application.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> Affectez uniquement `Development` à la variable d’environnement `ASPNETCORE_ENVIRONMENT` sur les serveurs de test et de préproduction non accessibles aux réseaux non approuvés, par exemple Internet.

## <a name="app_offlinehtm"></a>app_offline.htm

Si un fichier portant le nom *app_offline.htm* est détecté dans le répertoire racine d’une application, le module ASP.NET Core tente d’arrêter normalement l’application, puis interrompt le traitement des requêtes entrantes. Si l’application est toujours active après le nombre de secondes défini dans `shutdownTimeLimit`, le module ASP.NET Core met fin au processus en cours d’exécution.

Tant que le fichier *app_offline.htm* est présent, le module ASP.NET Core répond aux requêtes en renvoyant le contenu du fichier *app_offline.htm*. Lorsque le fichier *app_offline.htm* est supprimé, la requête suivante démarre l’application.

## <a name="start-up-error-page"></a>Page d’erreur de démarrage

Si le module ASP.NET Core ne peut pas lancer le processus backend ou que ce dernier démarre mais ne parvient pas à écouter sur le port configuré, une page de code d’état *502.5 - Échec du processus* s’affiche. Pour supprimer cette page et revenir à la page IIS de code d’état 502 par défaut, utilisez l’attribut `disableStartUpErrorPage`. Pour plus d’informations sur la configuration des messages d’erreur personnalisés, consultez [Erreurs HTTP \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).

## <a name="log-creation-and-redirection"></a>Création et redirection de journal

Le module ASP.NET Core redirige la sortie de console stdout et stderr vers le disque si les attributs `stdoutLogEnabled` et `stdoutLogFile` de l’élément `aspNetCore` sont définis. Tous les dossiers du chemin `stdoutLogFile` sont créés par le module au moment de la création du fichier journal. Le pool d’applications doit avoir un accès en écriture à l’emplacement auquel les journaux sont écrits (utilisez `IIS AppPool\<app_pool_name>` pour fournir les autorisations d’écriture).

Aucune rotation n’est appliquée aux journaux, sauf en cas de recyclage/redémarrage du processus. Il incombe à l’hébergeur de limiter l’espace disque utilisé par les journaux.

L’utilisation du journal stdout est recommandée uniquement pour résoudre les problèmes de démarrage d’application lors de l’hébergement sur IIS ou lors de l’utilisation [de la prise en charge au moment du développement pour IIS avec Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), et non pendant le débogage local et l’exécution de l’application avec IIS Express.

N’utilisez pas le journal stdout à des fins de journalisation d’application générale. Pour les opérations de journalisation courantes dans une application ASP.NET Core, utilisez une bibliothèque de journalisation qui limite la taille du fichier journal et applique une rotation aux journaux. Pour plus d’informations, consultez [Fournisseurs de journalisation tiers](xref:fundamentals/logging/index#third-party-logging-providers).

Un horodatage et une extension de fichier sont ajoutés automatiquement quand le fichier journal est créé. Le nom du fichier journal est créé en ajoutant l’horodatage, un ID de processus et une extension de fichier (*.log*) au dernier segment du chemin d'accès `stdoutLogFile` (généralement *stdout*), séparés par des traits de soulignement. Si le chemin d'accès `stdoutLogFile` se termine par *stdout*, un journal pour une application avec un PID de 1934 créé le 5/2/2018 à 19:42:32 affiche le nom de fichier *stdout_20180205194132_1934.log*.

L’exemple `aspNetCore` d’élément suivant configure la journalisation stdout sur le chemin d’accès relatif `.\log\` . Vérifiez que l’identité de l’utilisateur AppPool à l’autorisation d’écrire dans le chemin d’accès fourni.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

Lors de la publication d’une application pour le déploiement Azure App Service, le kit de développement logiciel (SDK) Web définit la `stdoutLogFile` valeur sur `\\?\%home%\LogFiles\stdout` . La `%home` variable d’environnement est prédéfinie pour les applications hébergées par Azure App service.

Pour créer des règles de filtre de journalisation, consultez les sections [configuration](xref:fundamentals/logging/index#log-filtering) et [filtrage des journaux](xref:fundamentals/logging/index#log-filtering) de la documentation ASP.net Core Logging.

Pour plus d’informations sur les formats de chemin d’accès, consultez [formats de chemin d’accès de fichier sur les systèmes Windows](/dotnet/standard/io/file-path-formats).

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>La configuration du proxy utilise le protocole HTTP et un jeton d’appariement

Le proxy créé entre le module ASP.NET Core et Kestrel utilise le protocole HTTP. Il n’existe aucun risque d’écoute clandestine du trafic entre le module et Kestrel à partir d’un emplacement sur le serveur.

Un jeton d’appariement est utilisé pour garantir que les demandes reçues par Kestrel ont été traitées par IIS et ne proviennent pas d’une autre source. Le jeton d’appariement est créé et défini dans une variable d’environnement (`ASPNETCORE_TOKEN`) par le module. Le jeton d’appariement est également défini dans un en-tête (`MS-ASPNETCORE-TOKEN`) sur toutes les demandes traitées. L'intergiciel IIS vérifie chaque demande qu’il reçoit pour confirmer que la valeur d’en-tête du jeton d'appariement correspond à la valeur de la variable d’environnement. Si les valeurs de jeton ne correspondent pas, la demande est connectée et rejetée. La variable d’environnement du jeton d'appariement et le trafic entre le module et Kestrel ne sont pas accessibles à partir d’un emplacement sur le serveur. Sans connaître la valeur du jeton d'appariement, une personne malveillante ne peut pas soumettre des demandes qui contournent la vérification de l’intergiciel IIS.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>Module ASP.NET Core avec une configuration partagée IIS

Le programme d’installation du module ASP.NET Core s’exécute avec les privilèges du compte **TrustedInstaller**. Comme le compte système local n’a pas l’autorisation de modifier le chemin de partage utilisé par la configuration partagée IIS, le programme d’installation génère une erreur d’accès refusé pendant la tentative de configuration des paramètres du module dans le fichier *applicationHost.config* du partage.

Lorsque vous utilisez une configuration partagée IIS, procédez comme suit :

1. Désactivez la configuration partagée IIS.
1. Exécutez le programme d’installation.
1. Exportez le fichier *applicationHost.config* mis à jour vers le partage.
1. Réactivez la configuration partagée IIS.

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Version du module et journaux du programme d’installation du bundle d’hébergement

Pour déterminer la version du module ASP.NET Core installé :

1. Sur le système hôte, accédez à *%windir%\System32\inetsrv*.
1. Recherchez le fichier *aspnetcore.dll*.
1. Cliquez avec le bouton droit sur le fichier, puis sélectionnez **Propriétés** dans le menu contextuel.
1. Sélectionnez l’onglet **Détails** . La version du **fichier** et la version du **produit** représentent la version installée du module.

Les journaux du programme d’installation du bundle d’hébergement pour le module se trouvent sur *C : \\ Users \\ % UserName% \\ AppData \\ local \\ temp*. Le fichier est nommé *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.

## <a name="module-schema-and-configuration-file-locations"></a>Emplacements des fichiers du module, du schéma et de configuration

### <a name="module"></a>Module

**IIS (x86/amd64) :**

* %windir%\System32\inetsrv\aspnetcore.dll

* %windir%\SysWOW64\inetsrv\aspnetcore.dll

**IIS Express (x86/amd64) :**

* %ProgramFiles%\IIS Express\aspnetcore.dll

* %ProgramFiles(x86)%\IIS Express\aspnetcore.dll

### <a name="schema"></a>schéma

**IIS**

* %windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml

**IIS Express**

* %ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml

### <a name="configuration"></a>Configuration

**IIS**

* %windir%\System32\inetsrv\config\applicationHost.config

**IIS Express**

* Visual Studio : {RACINE DE L’APPLICATION}\\.vs\config\applicationHost.config

* *iisexpress.exe* CLI : %PROFILUTILISATEUR%\Documents\IISExpress\config\applicationhost.config

Vous trouverez les fichiers en recherchant *aspnetcore* dans le fichier *applicationHost.config*.

::: moniker-end

## <a name="additional-resources"></a>Ressources supplémentaires

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* [Source de référence du Module ASP.net Core [branche par défaut (Master)]](https://github.com/dotnet/aspnetcore/tree/main/src/Servers/IIS/AspNetCoreModuleV2): utilisez la liste déroulante **branche** pour sélectionner une version spécifique (par exemple, `release/3.1` ).
* <xref:host-and-deploy/iis/modules>
