---
title: Héberger ASP.NET Core dans un service Windows
author: rick-anderson
description: Découvrez comment héberger une application ASP.NET Core dans un service Windows.
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: e5c7dd0e52f1246d3ac6ad9622573db4c276654b
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588813"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Héberger ASP.NET Core dans un service Windows

::: moniker range=">= aspnetcore-3.0"

Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS. Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

* [Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus](https://dotnet.microsoft.com/download)
* [PowerShell 6,2 ou version ultérieure](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>Modèle Service Worker

Le modèle Service Worker ASP.NET Core fournit un point de départ pour l’écriture d’applications de service durables. Pour utiliser le modèle en tant que base d’une application de service Windows :

1. Créez une application Service Worker à partir du modèle .NET Core.
1. Suivez l’aide fournie dans la section [Configuration d’application](#app-configuration) pour mettre à jour l’application Service Worker afin qu’elle puisse s’exécuter en tant que service Windows.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>la configuration d’une application ;

L’application requiert une référence de package pour [Microsoft. extensions. Hosting. services Windows,](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).

`IHostBuilder.UseWindowsService` est appelé lors de la génération de l’hôte. Si l’application s’exécute comme un service Windows, la méthode :

* définit la durée de vie de l’hôte sur `WindowsServiceLifetime` ;
* Affecte à la [racine du contenu](xref:fundamentals/index#content-root) la valeur [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory). Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root).
* Active la journalisation dans le journal des événements :
  * Le nom de l’application est utilisé comme nom de source par défaut.
  * Le niveau de journalisation par défaut est *Avertissement* ou supérieur pour une application basée sur un modèle de ASP.net core qui appelle `CreateDefaultBuilder` pour créer l’hôte.
  * Remplacez le niveau de journalisation par défaut par la `Logging:EventLog:LogLevel:Default` clé dans *appsettings.json* / *appSettings. { Environment}. JSON* ou un autre fournisseur de configuration.
  * Seuls les administrateurs peuvent créer des sources d’événement. Si une source d’événement ne peut pas être créée en utilisant le nom de l’application, un avertissement est consigné dans la source *Application* source et les journaux d’événements sont désactivés.

Dans `CreateHostBuilder` de *Program.cs*:

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Les exemples d’applications suivants accompagnent cette rubrique :

* Exemple de service de travail en arrière-plan : exemple d’application non Web basé sur le [modèle de service Worker](#worker-service-template) qui utilise des [services hébergés](xref:fundamentals/host/hosted-services) pour les tâches en arrière-plan.
* Exemple de App Service Web : Razor exemple d’application Web pages qui s’exécute en tant que service Windows avec les [services hébergés](xref:fundamentals/host/hosted-services) pour les tâches en arrière-plan.

Pour obtenir des conseils sur MVC, consultez les articles sous <xref:mvc/overview> et <xref:migration/22-to-30> .

## <a name="deployment-type"></a>Type de déploiement

Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

Pour un service basé sur une application Web qui utilise les Razor pages ou les infrastructures MVC, spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si le service exécute uniquement des tâches en arrière-plan (par exemple, les [services hébergés](xref:fundamentals/host/hosted-services)), spécifiez le kit de développement logiciel (SDK) Worker dans le fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Déploiement dépendant du framework

Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible. Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.

Si vous utilisez le [Kit de développement logiciel (SDK) Web](#sdk), un fichier de *web.config* , qui est normalement généré lors de la publication d’une application ASP.net Core, n’est pas nécessaire pour une application de services Windows. Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Déploiement autonome

Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte. Le runtime et les dépendances de l’application sont déployés avec l’application.

Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Pour publier pour plusieurs RID :

* Fournissez les RID dans une liste séparée par des points-virgules.
* Utilisez le nom de la propriété [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).

Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).

## <a name="service-user-account"></a>Compte d’utilisateur du service

Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.

Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.

Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.

Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).

Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés. Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Droits d’ouverture de session en tant que service

Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :

1. Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.
1. Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.
1. Ouvrez la stratégie **Ouvrir une session en tant que service**.
1. Sélectionnez **Ajouter un utilisateur ou un groupe**.
1. Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :
   1. Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.
   1. Sélectionnez **Avancé**. Sélectionnez **Rechercher maintenant**. Sélectionnez le compte d’utilisateur dans la liste. Sélectionnez **OK**. Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.
1. Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.

## <a name="create-and-manage-the-windows-service"></a>Créer et gérer le service Windows

### <a name="create-a-service"></a>Créer un service

Utilisez les commandes PowerShell pour enregistrer un service. À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: Chemin d’accès au dossier de l’application sur l’hôte (par exemple, `d:\myservice` ). N’incluez pas le fichier exécutable de l’application dans le chemin. Aucune barre oblique de fin n’est nécessaire.
* `{DOMAIN OR COMPUTER NAME\USER}`: Compte d’utilisateur du service (par exemple, `Contoso\ServiceUser` ).
* `{SERVICE NAME}`: Nom du service (par exemple, `MyService` ).
* `{EXE FILE PATH}`: Le chemin d’accès de l’exécutable de l’application (par exemple, `d:\myservice\myservice.exe` ). Incluez le nom de fichier de l’exécutable avec l’extension.
* `{DESCRIPTION}`: Description du service (par exemple, `My sample service` ).
* `{DISPLAY NAME}`: Nom d’affichage du service (par exemple, `My Service` ).

### <a name="start-a-service"></a>Démarrer un service

Démarrez un service avec la commande PowerShell 6 suivante :

```powershell
Start-Service -Name {SERVICE NAME}
```

La commande prend quelques secondes pour démarrer le service.

### <a name="determine-a-services-status"></a>Déterminer l’état d’un service

Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :

```powershell
Get-Service -Name {SERVICE NAME}
```

L’état est signalé comme étant l’une des valeurs suivantes :

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Arrêter un service

Arrêtez un service avec la commande Powershell 6 suivante :

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Supprimer un service

Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire. Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configuration des points de terminaison

Par défaut, ASP.NET Core est lié à `http://localhost:5000`. Configurez l’URL et le port en définissant la `ASPNETCORE_URLS` variable d’environnement.

Pour obtenir d’autres approches de configuration des ports et des URL, consultez l’article approprié sur le serveur :

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* <xref:fundamentals/servers/kestrel/endpoints>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

* <xref:fundamentals/servers/kestrel#endpoint-configuration>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <xref:fundamentals/servers/httpsys#configure-windows-server>

L’aide précédente couvre la prise en charge des points de terminaison HTTPs. Par exemple, configurez l’application pour HTTPs lorsque l’authentification est utilisée avec un service Windows.

> [!NOTE]
> L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.

## <a name="current-directory-and-content-root"></a>Répertoire actif et racine du contenu

Le répertoire de travail actuel retourné en appelant <xref:System.IO.Directory.GetCurrentDirectory%2A> pour un service Windows est le dossier *\\ \\ system32 de C : Windows* . Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres). Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>Utiliser ContentRootPath ou ContentRootFileProvider

Utilisez les éléments [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) ou <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> pour localiser les ressources d’une application.

Lorsque l’application s’exécute en tant que service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService%2A> définit <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> sur [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory).

Les fichiers de paramètres par défaut de l’application, *appsettings.json* et *appSettings. { Environment}. JSON*, sont chargés à partir de la racine de contenu de l’application en appelant [CreateDefaultBuilder lors](xref:fundamentals/host/generic-host#set-up-a-host)de la construction de l’hôte.

Pour les autres fichiers de paramètres chargés par le code du développeur dans <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> , il n’est pas nécessaire d’appeler <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A> . Dans l’exemple suivant, la *custom_settings.jssur* le fichier existe dans la racine de contenu de l’application et est chargée sans définir explicitement un chemin d’accès de base :

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

N’essayez pas d’utiliser <xref:System.IO.Directory.GetCurrentDirectory%2A> pour obtenir un chemin d’accès de ressource, car une application de service Windows retourne le dossier *C : \\ Windows \\ system32* comme répertoire actif.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Stocker les fichiers d’un service dans un emplacement approprié sur le disque

Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath%2A>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.

## <a name="troubleshoot"></a>Dépanner

Pour dépanner une application de service Windows, consultez <xref:test/troubleshoot> .

### <a name="common-errors"></a>Erreurs courantes

* Une version ancienne ou préliminaire de PowerShell est en cours d’utilisation.
* Le service inscrit n’utilise pas la sortie **publiée** de l’application à partir de la commande [dotnet Publish](/dotnet/core/tools/dotnet-publish) . La sortie de la commande [dotnet Build](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement d’applications. Les ressources publiées se trouvent dans l’un des dossiers suivants, selon le type de déploiement :
  * *bin/Release/{Target Framework}/Publish* (FDD)
  * *bin/Release/{Target Framework}/{Runtime identificateur}/Publish* (SCD)
* Le service n’est pas à l’État en cours d’exécution.
* Les chemins d’accès aux ressources que l’application utilise (par exemple, les certificats) sont incorrects. Le chemin d’accès de base d’un service Windows est *c : \\ Windows \\ system32*.
* L’utilisateur ne dispose pas de droits *d’ouverture de session en tant que service* .
* Le mot de passe de l’utilisateur a expiré ou a été transmis de manière incorrecte lors de l’exécution de la `New-Service` commande PowerShell.
* L’application requiert l’authentification ASP.NET Core, mais elle n’est pas configurée pour les connexions sécurisées (HTTPs).
* Le port de l’URL de requête est incorrect ou n’est pas correctement configuré dans l’application.

### <a name="system-and-application-event-logs"></a>Journaux des événements système et des applications

Accédez aux journaux des événements système et des applications :

1. Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .
1. Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.
1. Sélectionnez **système** pour ouvrir le journal des événements système. Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.
1. Recherchez les erreurs liées à l’application défectueuse.

### <a name="run-the-app-at-a-command-prompt"></a>Exécuter l’application depuis une invite de commandes

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux des événements. Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte. Pour consigner des détails supplémentaires à partir de l’application, diminuez le [niveau de journalisation](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l' [environnement de développement](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Effacer les caches de package

Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :

1. Supprimez les dossiers *bin* et *obj*.
1. Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.

   L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` . *NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).

1. Restaurez et regénérez le projet.
1. Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.

### <a name="slow-or-hanging-app"></a>Application lente ou bloquée

Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.

#### <a name="app-crashes-or-encounters-an-exception"></a>L’application cesse de fonctionner ou rencontre une exception

Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :

1. Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.
1. Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) avec le nom de l’exécutable de l’application :

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.
1. Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1) :

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement. Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.

> [!WARNING]
> Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>L’application se bloque, ne démarre pas ou s’exécute normalement

Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.

#### <a name="analyze-the-dump"></a>Analyser le fichier dump

Un fichier dump peut être analysé à l’aide de plusieurs approches. Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).

## <a name="additional-resources"></a>Ressources supplémentaires

::: moniker-end

::: moniker range=">= aspnetcore-5.0"
* [Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel/endpoints) (inclut la configuration de HTTPS et la prise en charge de SNI)
::: moniker-end
::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"
* [Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS. Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

* [Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus](https://dotnet.microsoft.com/download)
* [PowerShell 6,2 ou version ultérieure](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>la configuration d’une application ;

L’application nécessite des références de package pour [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) et [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Pour effectuer des tests et un débogage lors de l’exécution en dehors d’un service, ajoutez un code pour déterminer si l’application s’exécute comme un service ou comme une application console. Vérifiez si le débogueur est attaché ou si un switch `--console` est présent. Si l’une de ces deux conditions est remplie (l’application n’est pas exécutée en tant que service), appelez <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Si les conditions ne sont pas remplies (l’application est exécutée en tant que service) :

* Appelez <xref:System.IO.Directory.SetCurrentDirectory*> et utilisez un chemin vers l’emplacement publié de l’application. N’appelez pas <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir le chemin d’accès car une application Windows Service retourne le dossier *C:\\WINDOWS\\system32* lorsque <xref:System.IO.Directory.GetCurrentDirectory*> est appelée. Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root). Cette étape est effectuée avant la configuration de l’application dans `CreateWebHostBuilder`.
* Appelez <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pour exécuter l’application en tant que service.

Étant donné que le [fournisseur de configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider) nécessite des paires nom/valeur pour les arguments de ligne de commande, le switch `--console` est supprimé des arguments avant que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ne les reçoive.

Pour consigner des informations dans le journal des événements Windows, ajoutez le fournisseur EventLog à <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Définissez le niveau de journalisation à l’aide de clé `Logging:LogLevel:Default` dans le fichier *appsettings.Production.json*.

Dans l’exemple suivant, qui provient de l’exemple d’application, l’élément `RunAsCustomService` est appelé à la place de l’élément <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> afin de gérer les événements de durée de vie au sein de l’application. Pour plus d’informations, consultez la section [Gérer les événements de démarrage et d’arrêt](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Type de déploiement

Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

Pour un service basé sur une application Web qui utilise les Razor pages ou les infrastructures MVC, spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si le service exécute uniquement des tâches en arrière-plan (par exemple, les [services hébergés](xref:fundamentals/host/hosted-services)), spécifiez le kit de développement logiciel (SDK) Worker dans le fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Déploiement dépendant du framework

Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible. Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.

L' [identificateur Windows Runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contient la version cible de .NET Framework. Dans l’exemple suivant, le RID est défini sur `win7-x64`. La propriété `<SelfContained>` est définie sur `false`. Ces propriétés demandent au Kit de développement logiciel (SDK) de générer un fichier exécutable (*.exe*) pour Windows et une application qui dépend du framework .NET Core partagé.

Un fichier *web.config*, qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application de Windows Services. Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Déploiement autonome

Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte. Le runtime et les dépendances de l’application sont déployés avec l’application.

Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Pour publier pour plusieurs RID :

* Fournissez les RID dans une liste séparée par des points-virgules.
* Utilisez le nom de la propriété [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).

Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).

Une propriété `<SelfContained>` est définie sur `true` :

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Compte d’utilisateur du service

Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.

Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.

Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.

Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).

Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés. Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Droits d’ouverture de session en tant que service

Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :

1. Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.
1. Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.
1. Ouvrez la stratégie **Ouvrir une session en tant que service**.
1. Sélectionnez **Ajouter un utilisateur ou un groupe**.
1. Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :
   1. Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.
   1. Sélectionnez **Avancé**. Sélectionnez **Rechercher maintenant**. Sélectionnez le compte d’utilisateur dans la liste. Sélectionnez **OK**. Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.
1. Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.

## <a name="create-and-manage-the-windows-service"></a>Créer et gérer le service Windows

### <a name="create-a-service"></a>Créer un service

Utilisez les commandes PowerShell pour enregistrer un service. À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: Chemin d’accès au dossier de l’application sur l’hôte (par exemple, `d:\myservice` ). N’incluez pas le fichier exécutable de l’application dans le chemin. Aucune barre oblique de fin n’est nécessaire.
* `{DOMAIN OR COMPUTER NAME\USER}`: Compte d’utilisateur du service (par exemple, `Contoso\ServiceUser` ).
* `{SERVICE NAME}`: Nom du service (par exemple, `MyService` ).
* `{EXE FILE PATH}`: Le chemin d’accès de l’exécutable de l’application (par exemple, `d:\myservice\myservice.exe` ). Incluez le nom de fichier de l’exécutable avec l’extension.
* `{DESCRIPTION}`: Description du service (par exemple, `My sample service` ).
* `{DISPLAY NAME}`: Nom d’affichage du service (par exemple, `My Service` ).

### <a name="start-a-service"></a>Démarrer un service

Démarrez un service avec la commande PowerShell 6 suivante :

```powershell
Start-Service -Name {SERVICE NAME}
```

La commande prend quelques secondes pour démarrer le service.

### <a name="determine-a-services-status"></a>Déterminer l’état d’un service

Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :

```powershell
Get-Service -Name {SERVICE NAME}
```

L’état est signalé comme étant l’une des valeurs suivantes :

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Arrêter un service

Arrêtez un service avec la commande Powershell 6 suivante :

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Supprimer un service

Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Gérer les événements de démarrage et d’arrêt

Pour gérer les événements <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> et <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, procédez comme suit :

1. Créez une classe qui dérive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> à l’aide des méthodes `OnStarting`, `OnStarted` et `OnStopping` :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Créez une méthode d’extension pour <xref:Microsoft.AspNetCore.Hosting.IWebHost> qui transmet `CustomWebHostService` à <xref:System.ServiceProcess.ServiceBase.Run*> :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Dans `Program.Main`, appelez la méthode d’extension `RunAsCustomService` au lieu de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :

   ```csharp
   host.RunAsCustomService();
   ```

   Pour afficher l’emplacement de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> dans `Program.Main`, reportez-vous à l’exemple de code indiqué dans la section [Type de déploiement](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire. Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configuration des points de terminaison

Par défaut, ASP.NET Core est lié à `http://localhost:5000`. Configurez l’URL et le port en définissant la `ASPNETCORE_URLS` variable d’environnement.

Pour obtenir d’autres approches de configuration des ports et des URL, consultez l’article approprié sur le serveur :

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

L’aide précédente couvre la prise en charge des points de terminaison HTTPs. Par exemple, configurez l’application pour HTTPs lorsque l’authentification est utilisée avec un service Windows.

> [!NOTE]
> L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.

## <a name="current-directory-and-content-root"></a>Répertoire actif et racine du contenu

Le répertoire de travail actuel retourné en appelant <xref:System.IO.Directory.GetCurrentDirectory*> pour un service Windows est le dossier *\\ \\ system32 de C : Windows* . Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres). Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Définir le dossier de l’application comme chemin d’accès racine du contenu

La chaîne <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> correspond au même chemin que celui fourni à l’argument `binPath` lorsqu’un service est créé. Au lieu d’appeler `GetCurrentDirectory` pour créer des chemins d’accès aux fichiers de paramètres, appelez <xref:System.IO.Directory.SetCurrentDirectory*> avec le chemin d’accès à la [racine de contenu](xref:fundamentals/index#content-root)de l’application.

Dans `Program.Main`, définissez le chemin d’accès au dossier du fichier exécutable du service ainsi que le chemin d’accès pour établir la racine du contenu de l’application :

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Stocker les fichiers d’un service dans un emplacement approprié sur le disque

Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.

## <a name="troubleshoot"></a>Dépanner

Pour dépanner une application de service Windows, consultez <xref:test/troubleshoot> .

### <a name="common-errors"></a>Erreurs courantes

* Une version ancienne ou préliminaire de PowerShell est en cours d’utilisation.
* Le service inscrit n’utilise pas la sortie **publiée** de l’application à partir de la commande [dotnet Publish](/dotnet/core/tools/dotnet-publish) . La sortie de la commande [dotnet Build](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement d’applications. Les ressources publiées se trouvent dans l’un des dossiers suivants, selon le type de déploiement :
  * *bin/Release/{Target Framework}/Publish* (FDD)
  * *bin/Release/{Target Framework}/{Runtime identificateur}/Publish* (SCD)
* Le service n’est pas à l’État en cours d’exécution.
* Les chemins d’accès aux ressources que l’application utilise (par exemple, les certificats) sont incorrects. Le chemin d’accès de base d’un service Windows est *c : \\ Windows \\ system32*.
* L’utilisateur ne dispose pas de droits *d’ouverture de session en tant que service* .
* Le mot de passe de l’utilisateur a expiré ou a été transmis de manière incorrecte lors de l’exécution de la `New-Service` commande PowerShell.
* L’application requiert l’authentification ASP.NET Core, mais elle n’est pas configurée pour les connexions sécurisées (HTTPs).
* Le port de l’URL de requête est incorrect ou n’est pas correctement configuré dans l’application.

### <a name="system-and-application-event-logs"></a>Journaux des événements système et des applications

Accédez aux journaux des événements système et des applications :

1. Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .
1. Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.
1. Sélectionnez **système** pour ouvrir le journal des événements système. Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.
1. Recherchez les erreurs liées à l’application défectueuse.

### <a name="run-the-app-at-a-command-prompt"></a>Exécuter l’application depuis une invite de commandes

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux des événements. Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte. Pour consigner des détails supplémentaires à partir de l’application, diminuez le [niveau de journalisation](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l' [environnement de développement](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Effacer les caches de package

Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :

1. Supprimez les dossiers *bin* et *obj*.
1. Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.

   L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` . *NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).

1. Restaurez et regénérez le projet.
1. Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.

### <a name="slow-or-hanging-app"></a>Application lente ou bloquée

Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.

#### <a name="app-crashes-or-encounters-an-exception"></a>L’application cesse de fonctionner ou rencontre une exception

Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :

1. Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.
1. Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) avec le nom de l’exécutable de l’application :

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.
1. Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) :

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement. Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.

> [!WARNING]
> Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>L’application se bloque, ne démarre pas ou s’exécute normalement

Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.

#### <a name="analyze-the-dump"></a>Analyser le fichier dump

Un fichier dump peut être analysé à l’aide de plusieurs approches. Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Une application ASP.NET Core peut être hébergée sur Windows en tant que [service Windows](/dotnet/framework/windows-services/introduction-to-windows-service-applications) sans utiliser IIS. Lorsqu’elle est hébergée en tant que service Windows, l’application se lance automatiquement après le redémarrage du serveur.

[Afficher ou télécharger l’exemple de code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/windows-service/samples) ([procédure de téléchargement](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Prérequis

* [Kit de développement logiciel (SDK) ASP.NET Core 2.1 ou plus](https://dotnet.microsoft.com/download)
* [PowerShell 6,2 ou version ultérieure](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>la configuration d’une application ;

L’application nécessite des références de package pour [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) et [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).

Pour effectuer des tests et un débogage lors de l’exécution en dehors d’un service, ajoutez un code pour déterminer si l’application s’exécute comme un service ou comme une application console. Vérifiez si le débogueur est attaché ou si un switch `--console` est présent. Si l’une de ces deux conditions est remplie (l’application n’est pas exécutée en tant que service), appelez <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>. Si les conditions ne sont pas remplies (l’application est exécutée en tant que service) :

* Appelez <xref:System.IO.Directory.SetCurrentDirectory*> et utilisez un chemin vers l’emplacement publié de l’application. N’appelez pas <xref:System.IO.Directory.GetCurrentDirectory*> pour obtenir le chemin d’accès car une application Windows Service retourne le dossier *C:\\WINDOWS\\system32* lorsque <xref:System.IO.Directory.GetCurrentDirectory*> est appelée. Pour plus d’informations, consultez la section [Répertoire actif et racine du contenu](#current-directory-and-content-root). Cette étape est effectuée avant la configuration de l’application dans `CreateWebHostBuilder`.
* Appelez <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> pour exécuter l’application en tant que service.

Étant donné que le [fournisseur de configuration de ligne de commande](xref:fundamentals/configuration/index#command-line-configuration-provider) nécessite des paires nom/valeur pour les arguments de ligne de commande, le switch `--console` est supprimé des arguments avant que <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ne les reçoive.

Pour consigner des informations dans le journal des événements Windows, ajoutez le fournisseur EventLog à <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>. Définissez le niveau de journalisation à l’aide de clé `Logging:LogLevel:Default` dans le fichier *appsettings.Production.json*.

Dans l’exemple suivant, qui provient de l’exemple d’application, l’élément `RunAsCustomService` est appelé à la place de l’élément <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> afin de gérer les événements de durée de vie au sein de l’application. Pour plus d’informations, consultez la section [Gérer les événements de démarrage et d’arrêt](#handle-starting-and-stopping-events).

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Type de déploiement

Pour des informations et des conseils sur les scénarios de déploiement, consultez [Déploiement d’applications .NET Core](/dotnet/core/deploying/).

### <a name="sdk"></a>SDK

Pour un service basé sur une application Web qui utilise les Razor pages ou les infrastructures MVC, spécifiez le kit de développement logiciel (SDK) Web dans le fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Si le service exécute uniquement des tâches en arrière-plan (par exemple, les [services hébergés](xref:fundamentals/host/hosted-services)), spécifiez le kit de développement logiciel (SDK) Worker dans le fichier projet :

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Déploiement dépendant du framework

Un déploiement dépendant du framework s’appuie sur la présence d’une version partagée à l’échelle du système de .NET Core sur le système cible. Lorsque vous effectuez le scénario de déploiement dépendant du framework en suivant les conseils du présent article, le Kit de développement logiciel (SDK) produit un fichier exécutable (*.exe*), appelé *fichier exécutable dépendant du framework*.

L' [identificateur Windows Runtime (RID)](/dotnet/core/rid-catalog) ( [\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier) ) contient la version cible de .NET Framework. Dans l’exemple suivant, le RID est défini sur `win7-x64`. La propriété `<SelfContained>` est définie sur `false`. Ces propriétés demandent au Kit de développement logiciel (SDK) de générer un fichier exécutable (*.exe*) pour Windows et une application qui dépend du framework .NET Core partagé.

La propriété `<UseAppHost>` est définie sur `true`. Cette propriété fournit au service un chemin d’activation (un fichier exécutable *.exe*) pour un déploiement dépendant du framework (FDD).

Un fichier *web.config*, qui est normalement produit lors de la publication d’une application ASP.NET Core, n’est pas nécessaire pour une application de Windows Services. Pour désactiver la création d’un fichier *web.config*, ajoutez la propriété `<IsTransformWebConfigDisabled>` définie sur `true`.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Déploiement autonome

Un déploiement autonome ne s’appuie sur la présence d’aucune infrastructure partagée sur le système hôte. Le runtime et les dépendances de l’application sont déployés avec l’application.

Un [identificateur de runtime (RID)](/dotnet/core/rid-catalog) Windows est inclus dans l’élément `<PropertyGroup>` qui contient la version cible de .NET Framework :

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Pour publier pour plusieurs RID :

* Fournissez les RID dans une liste séparée par des points-virgules.
* Utilisez le nom de la propriété [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (pluriel).

Pour plus d’informations, consultez le [Catalogue RID .NET Core](/dotnet/core/rid-catalog).

Une propriété `<SelfContained>` est définie sur `true` :

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Compte d’utilisateur du service

Pour créer un compte d’utilisateur du service, utilisez la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) depuis un interpréteur de commandes d’administration PowerShell 6.

Dans la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) ou plus :

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Dans un système d’exploitation Windows antérieur à la Mise à jour de Windows 10 d’octobre 2018 (version 1809/build 10.0.17763) :

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

Fournissez un [mot de passe fort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) à l’invite.

Le compte n’expire pas, sauf si le paramètre `-AccountExpires` est fourni à la cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) avec un <xref:System.DateTime> d’expiration.

Pour plus d’informations, voir [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) et [Comptes d’utilisateurs de service](/windows/desktop/services/service-user-accounts).

Une approche alternative à la gestion des utilisateurs lors de l’utilisation d’Active Directory consiste à utiliser des Comptes de service administrés. Pour plus d’informations, consultez [Vue d’ensemble des comptes de service administrés de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).

## <a name="log-on-as-a-service-rights"></a>Droits d’ouverture de session en tant que service

Pour établir des droits *d’ouverture de session en tant que service* pour un compte d’utilisateur de service, procédez comme suit :

1. Ouvrez l’éditeur de stratégie de sécurité locale en exécutant le fichier *secpool.msc*.
1. Développez le nœud **Stratégies locales** et sélectionnez **Attribution des droits utilisateur**.
1. Ouvrez la stratégie **Ouvrir une session en tant que service**.
1. Sélectionnez **Ajouter un utilisateur ou un groupe**.
1. Fournissez le nom d’objet (compte d’utilisateur) avec l’une des approches suivantes :
   1. Tapez le compte d’utilisateur (`{DOMAIN OR COMPUTER NAME\USER}`) dans le champ du nom d’objet et sélectionnez **OK** pour ajouter l’utilisateur à la stratégie.
   1. Sélectionnez **Avancé**. Sélectionnez **Rechercher maintenant**. Sélectionnez le compte d’utilisateur dans la liste. Sélectionnez **OK**. Cliquez à nouveau sur **OK** pour ajouter l’utilisateur à la stratégie.
1. Sélectionnez **OK** ou **Appliquer** pour accepter les modifications.

## <a name="create-and-manage-the-windows-service"></a>Créer et gérer le service Windows

### <a name="create-a-service"></a>Créer un service

Utilisez les commandes PowerShell pour enregistrer un service. À partir d’un interpréteur de commandes d’administration PowerShell 6, exécutez les commandes suivantes :

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`: Chemin d’accès au dossier de l’application sur l’hôte (par exemple, `d:\myservice` ). N’incluez pas le fichier exécutable de l’application dans le chemin. Aucune barre oblique de fin n’est nécessaire.
* `{DOMAIN OR COMPUTER NAME\USER}`: Compte d’utilisateur du service (par exemple, `Contoso\ServiceUser` ).
* `{SERVICE NAME}`: Nom du service (par exemple, `MyService` ).
* `{EXE FILE PATH}`: Le chemin d’accès de l’exécutable de l’application (par exemple, `d:\myservice\myservice.exe` ). Incluez le nom de fichier de l’exécutable avec l’extension.
* `{DESCRIPTION}`: Description du service (par exemple, `My sample service` ).
* `{DISPLAY NAME}`: Nom d’affichage du service (par exemple, `My Service` ).

### <a name="start-a-service"></a>Démarrer un service

Démarrez un service avec la commande PowerShell 6 suivante :

```powershell
Start-Service -Name {SERVICE NAME}
```

La commande prend quelques secondes pour démarrer le service.

### <a name="determine-a-services-status"></a>Déterminer l’état d’un service

Pour vérifier l’état d’un service, utilisez la commande PowerShell 6 suivante :

```powershell
Get-Service -Name {SERVICE NAME}
```

L’état est signalé comme étant l’une des valeurs suivantes :

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Arrêter un service

Arrêtez un service avec la commande Powershell 6 suivante :

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Supprimer un service

Après un court délai pour arrêter un service, supprimez-le avec la commande PowerShell 6 suivante :

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Gérer les événements de démarrage et d’arrêt

Pour gérer les événements <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> et <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>, procédez comme suit :

1. Créez une classe qui dérive de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> à l’aide des méthodes `OnStarting`, `OnStarted` et `OnStopping` :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Créez une méthode d’extension pour <xref:Microsoft.AspNetCore.Hosting.IWebHost> qui transmet `CustomWebHostService` à <xref:System.ServiceProcess.ServiceBase.Run*> :

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. Dans `Program.Main`, appelez la méthode d’extension `RunAsCustomService` au lieu de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> :

   ```csharp
   host.RunAsCustomService();
   ```

   Pour afficher l’emplacement de <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> dans `Program.Main`, reportez-vous à l’exemple de code indiqué dans la section [Type de déploiement](#deployment-type).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Scénarios avec un serveur proxy et un équilibreur de charge

Les services qui interagissent avec les requêtes provenant d’Internet ou d’un réseau d’entreprise et qui se trouvent derrière un proxy ou équilibreur de charge peuvent nécessiter une configuration supplémentaire. Pour plus d’informations, consultez <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Configuration des points de terminaison

Par défaut, ASP.NET Core est lié à `http://localhost:5000`. Configurez l’URL et le port en définissant la `ASPNETCORE_URLS` variable d’environnement.

Pour obtenir d’autres approches de configuration des ports et des URL, consultez l’article approprié sur le serveur :

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

L’aide précédente couvre la prise en charge des points de terminaison HTTPs. Par exemple, configurez l’application pour HTTPs lorsque l’authentification est utilisée avec un service Windows.

> [!NOTE]
> L’utilisation du certificat de développement HTTPS ASP.NET Core pour sécuriser un point de terminaison de service n’est pas prise en charge.

## <a name="current-directory-and-content-root"></a>Répertoire actif et racine du contenu

Le répertoire de travail actuel retourné en appelant <xref:System.IO.Directory.GetCurrentDirectory*> pour un service Windows est le dossier *\\ \\ system32 de C : Windows* . Le dossier *system32* n’est pas un emplacement approprié pour stocker les fichiers d’un service (tels que les fichiers de paramètres). Utilisez une des approches suivantes pour gérer les ressources ainsi que les fichiers de paramètres d’un service, et y accéder.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>Définir le dossier de l’application comme chemin d’accès racine du contenu

La chaîne <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> correspond au même chemin que celui fourni à l’argument `binPath` lorsqu’un service est créé. Au lieu d’appeler `GetCurrentDirectory` pour créer des chemins d’accès aux fichiers de paramètres, appelez <xref:System.IO.Directory.SetCurrentDirectory*> avec le chemin d’accès à la [racine de contenu](xref:fundamentals/index#content-root)de l’application.

Dans `Program.Main`, définissez le chemin d’accès au dossier du fichier exécutable du service ainsi que le chemin d’accès pour établir la racine du contenu de l’application :

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Stocker les fichiers d’un service dans un emplacement approprié sur le disque

Spécifiez un chemin absolu avec <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>, si vous utilisez <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, vers le dossier contenant les fichiers.

## <a name="troubleshoot"></a>Dépanner

Pour dépanner une application de service Windows, consultez <xref:test/troubleshoot> .

### <a name="common-errors"></a>Erreurs courantes

* Une version ancienne ou préliminaire de PowerShell est en cours d’utilisation.
* Le service inscrit n’utilise pas la sortie **publiée** de l’application à partir de la commande [dotnet Publish](/dotnet/core/tools/dotnet-publish) . La sortie de la commande [dotnet Build](/dotnet/core/tools/dotnet-build) n’est pas prise en charge pour le déploiement d’applications. Les ressources publiées se trouvent dans l’un des dossiers suivants, selon le type de déploiement :
  * *bin/Release/{Target Framework}/Publish* (FDD)
  * *bin/Release/{Target Framework}/{Runtime identificateur}/Publish* (SCD)
* Le service n’est pas à l’État en cours d’exécution.
* Les chemins d’accès aux ressources que l’application utilise (par exemple, les certificats) sont incorrects. Le chemin d’accès de base d’un service Windows est *c : \\ Windows \\ system32*.
* L’utilisateur ne dispose pas de droits *d’ouverture de session en tant que service* .
* Le mot de passe de l’utilisateur a expiré ou a été transmis de manière incorrecte lors de l’exécution de la `New-Service` commande PowerShell.
* L’application requiert l’authentification ASP.NET Core, mais elle n’est pas configurée pour les connexions sécurisées (HTTPs).
* Le port de l’URL de requête est incorrect ou n’est pas correctement configuré dans l’application.

### <a name="system-and-application-event-logs"></a>Journaux des événements système et des applications

Accédez aux journaux des événements système et des applications :

1. Ouvrez le menu Démarrer, recherchez *Observateur d’événements*, puis sélectionnez l’application **Observateur d’événements** .
1. Dans **Observateur d’événements**, ouvrez le nœud **Journaux Windows**.
1. Sélectionnez **système** pour ouvrir le journal des événements système. Sélectionnez **Application** pour ouvrir le Journal des événements de l’application.
1. Recherchez les erreurs liées à l’application défectueuse.

### <a name="run-the-app-at-a-command-prompt"></a>Exécuter l’application depuis une invite de commandes

De nombreuses erreurs de démarrage ne produisent pas d’informations utiles dans les journaux des événements. Vous pouvez trouver la cause de certaines erreurs en exécutant l’application depuis une invite de commandes sur le système hôte. Pour consigner des détails supplémentaires à partir de l’application, diminuez le [niveau de journalisation](xref:fundamentals/logging/index#log-level) ou exécutez l’application dans l' [environnement de développement](xref:fundamentals/environments).

### <a name="clear-package-caches"></a>Effacer les caches de package

Une application fonctionnelle peut échouer immédiatement après la mise à niveau de la kit SDK .NET Core sur l’ordinateur de développement ou la modification des versions de package dans l’application. Dans certains cas, les packages incohérents peuvent bloquer une application quand vous effectuez des mises à niveau majeures. Vous pouvez résoudre la plupart de ces problèmes en suivant les instructions suivantes :

1. Supprimez les dossiers *bin* et *obj*.
1. Effacez les caches de package en exécutant [dotnet NuGet LOCALS tout--Clear](/dotnet/core/tools/dotnet-nuget-locals) dans une interface de commande.

   L’effacement des caches de package peut également être effectué à l’aide de l’outil [nuget.exe](https://www.nuget.org/downloads) et en exécutant la commande `nuget locals all -clear` . *NuGet.exe* n’étant pas une installation fournie avec le système d’exploitation de bureau Windows, il doit être obtenu séparément à partir du [site web de NuGet](https://www.nuget.org/downloads).

1. Restaurez et regénérez le projet.
1. Supprimez tous les fichiers du dossier de déploiement sur le serveur avant de redéployer l’application.

### <a name="slow-or-hanging-app"></a>Application lente ou bloquée

Un *vidage sur incident* est un instantané de la mémoire du système et peut aider à déterminer la cause d’un incident d’application, d’un échec de démarrage ou d’une application lente.

#### <a name="app-crashes-or-encounters-an-exception"></a>L’application cesse de fonctionner ou rencontre une exception

Obtenez un fichier dump et analysez-le depuis le [Rapport d'erreurs Windows](/windows/desktop/wer/windows-error-reporting) :

1. Créez un dossier pour accueillir les fichiers d’image mémoire dans `c:\dumps`.
1. Exécutez le [script PowerShell EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) avec le nom de l’exécutable de l’application :

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Exécutez l’application en reproduisant les conditions ayant entraîné l’incident.
1. Une fois l’incident survenu, exécutez le [script PowerShell DisableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) :

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Après l’arrêt de l’application et après avoir terminé la collection dump, l’application peut être fermée normalement. Le script PowerShell configure le rapport d’erreurs Windows pour collecter jusqu'à cinq fichiers dump par application.

> [!WARNING]
> Les fichiers dump d’incident peuvent occuper plus d’espace disque (jusqu’à plusieurs gigaoctets par fichier).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>L’application se bloque, ne démarre pas ou s’exécute normalement

Quand une application *se bloque* (cesse de répondre mais ne se bloque pas), échoue pendant le démarrage ou s’exécute normalement, consultez [fichiers de vidage en mode utilisateur : choix de l’outil le mieux](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) adapté pour sélectionner un outil approprié pour produire le vidage.

#### <a name="analyze-the-dump"></a>Analyser le fichier dump

Un fichier dump peut être analysé à l’aide de plusieurs approches. Pour plus d’informations, consultez [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file) (Analyser un fichier dump en mode utilisateur).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Configuration de point de terminaison Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration) (inclut la configuration de HTTPS et la prise en charge de SNI)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
