---
title: Bundle d’hébergement
author: rick-anderson
description: Découvrez comment configurer le bundle d’hébergement .NET Core.
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343635"
---
# <a name="the-net-core-hosting-bundle"></a>Le bundle d’hébergement .NET Core

Le bundle d’hébergement .NET Core est un programme d’installation pour le Runtime .NET Core et le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module). Le bundle permet l’exécution d’applications ASP.NET Core avec IIS.

## <a name="install-the-net-core-hosting-bundle"></a>Installer le bundle d’hébergement .NET Core

> [!IMPORTANT]
> Si le bundle d’hébergement est installé avant IIS, l’installation du bundle doit être réparée. Après avoir installé IIS, réexécutez le programme d’installation du bundle d’hébergement.
>
> Si le bundle d’hébergement est installé après l’installation de la version 64 bits (x 64) de .NET Core, les SDK peuvent apparaître manquants ([Aucun SDK .NET Core n’a été détecté](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Pour résoudre le problème, consultez <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

## <a name="direct-download-current-version"></a>Téléchargement direct (version actuelle)

Téléchargez le programme d’installation à l’aide du lien suivant :

[Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a>Visual C++ la spécification redistribuable

Dans les versions antérieures de Windows, par exemple Windows Server 2012 R2, installez Visual Studio C++ 2015, 2017, 2019 Redistributable. Dans le cas contraire, un message d’erreur confus dans le journal des événements Windows indique que `The data is the error.`

[Package redistribuable](https://aka.ms/vs/16/release/vc_redist.x64.exe) 
 x64 et C++ actuel [Package redistribuable x86 vs C++ actuel](https://aka.ms/vs/16/release/vc_redist.x86.exe)

## <a name="earlier-versions-of-the-installer"></a>Versions antérieures du programme d’installation

Pour obtenir une version antérieure du programme d’installation :

1. Accédez à la page [Télécharger .net Core](https://dotnet.microsoft.com/download/dotnet-core) .
1. Sélectionnez la version .NET Core de votre choix.
1. Dans la colonne **Run apps - Runtime** , recherchez la ligne de la version du runtime .NET Core souhaitée.
1. Téléchargez le programme d’installation à l’aide du lien d' **hébergement** .

> [!WARNING]
> Certains programmes d’installation contiennent des versions qui sont arrivées à leur fin de vie (EOL) et qui ne sont plus prises en charge par Microsoft. Pour plus d’informations, consultez la [politique de support](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="options"></a>Options

1. Les paramètres suivants sont disponibles lorsque vous exécutez le programme d’installation à partir d’un shell de commande administrateur :

   * `OPT_NO_ANCM=1`: Ignorez l’installation du module ASP.NET Core.
   * `OPT_NO_RUNTIME=1`: Ignorez l’installation du Runtime .NET Core. Utilisé lorsque le serveur héberge uniquement [des déploiements autonomes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_SHAREDFX=1`: Ignorez l’installation du Framework partagé ASP.NET (runtime ASP.NET). Utilisé lorsque le serveur héberge uniquement [des déploiements autonomes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).
   * `OPT_NO_X86=1`: Ignorez l’installation des runtimes x86. Utilisez ce paramètre lorsque vous savez que vous n’hébergerez pas d’applications 32 bits. Si vous n’excluez pas d’avoir à héberger des applications 32 bits et 64 bits dans le futur, n'utilisez pas ce paramètre et installez les deux runtimes.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Désactive la vérification de l’utilisation d’une configuration partagée IIS lorsque la configuration partagée ( `applicationHost.config` ) se trouve sur le même ordinateur que l’installation d’IIS. *Disponible uniquement pour les programmes d’installation du pack d’hébergement ASP.NET Core 2.2 ou version ultérieure.* Pour plus d'informations, consultez <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.

> [!NOTE]
> Pour plus d’informations sur la configuration partagée IIS, consultez [Module ASP.NET Core avec configuration partagée des services Internet (IIS)](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="restart-iis"></a>Redémarrer IIS

Une fois le bundle d’hébergement installé, un redémarrage manuel d’IIS peut être nécessaire. Par exemple, il `dotnet` se peut que les outils de l’interface CLI (commande) n’existent pas sur le chemin d’accès pour l’exécution des processus de travail IIS.

Pour arrêter et démarrer manuellement IIS, exécutez les commandes suivantes dans un interpréteur de commandes avec élévation de privilèges :

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Version du module et journaux du programme d’installation du bundle d’hébergement

Pour déterminer la version du module ASP.NET Core installé :

1. Sur le système d’hébergement, accédez à `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .
1. Localisez le `aspnetcorev2.dll` fichier.
1. Cliquez avec le bouton droit sur le fichier, puis sélectionnez **Propriétés** dans le menu contextuel.
1. Sélectionnez l’onglet **Détails** . La version du **fichier** et la version du **produit** représentent la version installée du module.

Les journaux du programme d’installation du bundle d’hébergement pour le module sont disponibles à l’adresse `C:\Users\%UserName%\AppData\Local\Temp` . Le fichier est nommé `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , où l’espace réservé `{TIMESTAMP}` est l’horodateur du fichier.
