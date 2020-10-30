---
title: Bundle d’hébergement
author: rick-anderson
description: Découvrez comment configurer le bundle d’hébergement .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: ecf3dd45575390eee263a275e7f1fb9ec50011bb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058439"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="e1d4d-103">Le bundle d’hébergement .NET Core</span><span class="sxs-lookup"><span data-stu-id="e1d4d-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e1d4d-104">Le bundle d’hébergement .NET Core est un programme d’installation pour le Runtime .NET Core et le [Module ASP.net Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="e1d4d-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="e1d4d-105">Le bundle permet l’exécution d’applications ASP.NET Core avec IIS.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="e1d4d-106">Installer le bundle d’hébergement .NET Core</span><span class="sxs-lookup"><span data-stu-id="e1d4d-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e1d4d-107">Si le bundle d’hébergement est installé avant IIS, l’installation du bundle doit être réparée.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="e1d4d-108">Après avoir installé IIS, réexécutez le programme d’installation du bundle d’hébergement.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="e1d4d-109">Si le bundle d’hébergement est installé après l’installation de la version 64 bits (x 64) de .NET Core, les SDK peuvent apparaître manquants ([Aucun SDK .NET Core n’a été détecté](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="e1d4d-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="e1d4d-110">Pour résoudre le problème, consultez <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="e1d4d-111">Téléchargement direct (version actuelle)</span><span class="sxs-lookup"><span data-stu-id="e1d4d-111">Direct download (current version)</span></span>

<span data-ttu-id="e1d4d-112">Téléchargez le programme d’installation à l’aide du lien suivant :</span><span class="sxs-lookup"><span data-stu-id="e1d4d-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="e1d4d-113">Programme d’installation du bundle d’hébergement .NET Core actuel (téléchargement direct)</span><span class="sxs-lookup"><span data-stu-id="e1d4d-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="e1d4d-114">Versions antérieures du programme d’installation</span><span class="sxs-lookup"><span data-stu-id="e1d4d-114">Earlier versions of the installer</span></span>

<span data-ttu-id="e1d4d-115">Pour obtenir une version antérieure du programme d’installation :</span><span class="sxs-lookup"><span data-stu-id="e1d4d-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="e1d4d-116">Accédez à la page [Télécharger .net Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="e1d4d-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="e1d4d-117">Sélectionnez la version .NET Core de votre choix.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="e1d4d-118">Dans la colonne **Run apps - Runtime** , recherchez la ligne de la version du runtime .NET Core souhaitée.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="e1d4d-119">Téléchargez le programme d’installation à l’aide du lien d' **hébergement** .</span><span class="sxs-lookup"><span data-stu-id="e1d4d-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="e1d4d-120">Certains programmes d’installation contiennent des versions qui sont arrivées à leur fin de vie (EOL) et qui ne sont plus prises en charge par Microsoft.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="e1d4d-121">Pour plus d’informations, consultez la [politique de support](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="e1d4d-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="e1d4d-122">Installer le bundle d’hébergement</span><span class="sxs-lookup"><span data-stu-id="e1d4d-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="e1d4d-123">Exécutez le programme d’installation sur le serveur.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-123">Run the installer on the server.</span></span> <span data-ttu-id="e1d4d-124">Les paramètres suivants sont disponibles lorsque vous exécutez le programme d’installation à partir d’un shell de commande administrateur :</span><span class="sxs-lookup"><span data-stu-id="e1d4d-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="e1d4d-125">`OPT_NO_ANCM=1`: Ignorez l’installation du module ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="e1d4d-126">`OPT_NO_RUNTIME=1`: Ignorez l’installation du Runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="e1d4d-127">Utilisé lorsque le serveur héberge uniquement [des déploiements autonomes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="e1d4d-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e1d4d-128">`OPT_NO_SHAREDFX=1`: Ignorez l’installation du Framework partagé ASP.NET (runtime ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="e1d4d-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="e1d4d-129">Utilisé lorsque le serveur héberge uniquement [des déploiements autonomes (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="e1d4d-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="e1d4d-130">`OPT_NO_X86=1`: Ignorez l’installation des runtimes x86.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="e1d4d-131">Utilisez ce paramètre lorsque vous savez que vous n’hébergerez pas d’applications 32 bits.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="e1d4d-132">Si vous n’excluez pas d’avoir à héberger des applications 32 bits et 64 bits dans le futur, n'utilisez pas ce paramètre et installez les deux runtimes.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="e1d4d-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Désactive la vérification de l’utilisation d’une configuration partagée IIS lorsque la configuration partagée ( `applicationHost.config` ) se trouve sur le même ordinateur que l’installation d’IIS.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="e1d4d-134">*Disponible uniquement pour les programmes d’installation du pack d’hébergement ASP.NET Core 2.2 ou version ultérieure.*</span><span class="sxs-lookup"><span data-stu-id="e1d4d-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="e1d4d-135">Pour plus d'informations, consultez <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="e1d4d-136">Redémarrez le système ou exécutez les commandes suivantes dans une interface de commande :</span><span class="sxs-lookup"><span data-stu-id="e1d4d-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="e1d4d-137">Le redémarrage d’IIS récupère une modification apportée au CHEMIN D’ACCÈS du système, qui est une variable d’environnement, par le programme d’installation.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="e1d4d-138">ASP.NET Core n’adopte pas le comportement de restauration par progression pour les mises à jour correctives des packages d’infrastructure partagés.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="e1d4d-139">Après la mise à niveau de l’infrastructure partagée en installant un nouveau bundle d’hébergement, redémarrez le système ou exécutez les commandes suivantes dans un interpréteur de commandes :</span><span class="sxs-lookup"><span data-stu-id="e1d4d-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="e1d4d-140">Pour plus d’informations sur la configuration partagée IIS, consultez [Module ASP.NET Core avec configuration partagée des services Internet (IIS)](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="e1d4d-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="e1d4d-141">Version du module et journaux du programme d’installation du bundle d’hébergement</span><span class="sxs-lookup"><span data-stu-id="e1d4d-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="e1d4d-142">Pour déterminer la version du module ASP.NET Core installé :</span><span class="sxs-lookup"><span data-stu-id="e1d4d-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="e1d4d-143">Sur le système d’hébergement, accédez à `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .</span><span class="sxs-lookup"><span data-stu-id="e1d4d-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="e1d4d-144">Localisez le `aspnetcorev2.dll` fichier.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="e1d4d-145">Cliquez avec le bouton droit sur le fichier, puis sélectionnez **Propriétés** dans le menu contextuel.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="e1d4d-146">Sélectionnez l’onglet **Détails** . La version du **fichier** et la version du **produit** représentent la version installée du module.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="e1d4d-147">Les journaux du programme d’installation du bundle d’hébergement pour le module sont disponibles à l’adresse `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="e1d4d-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="e1d4d-148">Le fichier est nommé `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , où l’espace réservé `{TIMESTAMP}` est l’horodateur du fichier.</span><span class="sxs-lookup"><span data-stu-id="e1d4d-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
